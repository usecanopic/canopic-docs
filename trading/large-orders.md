---
description: How market makers bid for big orders with signed quotes, and how the best one settles atomically on-chain.
---

# Large orders and signed maker quotes

The RFQ path steps in under two conditions: an order is bigger than the vault clip, or a maker beats the vault's price. Off-chain, makers produce signed prices that expire quickly; on-chain, the taker executes the winner atomically. Because producing a signature is free for the maker, quotes can flow continuously, and gas is paid only on an actual fill.

## Fields in a maker quote

Quotes use EIP-712 typed data with this structure:

| Field | Meaning |
| --- | --- |
| `maker` | Signing address, which must hold a `MAKER` attestation |
| `tokenIn`, `tokenOut` | The asset pair; USDG is always one side |
| `amountIn`, `amountOut` | Exact size and exact price, with no partial fills allowed |
| `taker` | The requester's address, or zero to let any taker use it |
| `expiry` | Unix time in seconds; quotes live for seconds, not minutes |
| `nonce` | Usable once, and can be cancelled in batches |

Each incoming request is fanned out by the quote service to the full set of connected makers. After a collection window lasting a few hundred milliseconds, the top maker price is returned next to the vault price. When the router executes, the trader gets whichever of the two is better.

## Settlement

Fills are settled by calling `RfqSettler.settle(quote, signature)`. The contract verifies all of the following before any asset moves:

* the maker's signature and the maker's attestation;
* whether the taker holds `TRADER` status;
* that the nonce is valid;
* that the quote has not expired;
* where the implied price lands relative to the [oracle band](price-formula.md), which it may not exceed.

When every check passes, assets travel directly between the two sides. `tokenIn` passes from taker to maker and `tokenOut` from maker to taker, while the `ProtocolTreasury` collects the RFQ fee. The maker's side is drawn from the maker's own wallet through a standing Permit2 allowance, which means Canopic never takes custody of funds at any point in the process.

A settlement reverts in three cases: the quote has expired, its nonce was already used or cancelled, or the price has moved outside the band by the time the transaction is included. A reverted fill costs the maker nothing, apart from any hedge it may have opened.

## What draws makers in

1. **Flow they can hedge.** Size and direction are known up front for every request, and each asset has a liquid underlying. Take 200,000 USDG of tokenized NVDA filled while the exchange is open: the maker can lay off that risk in seconds; outside market hours, it can choose to price the weekend gap into the quote.
2. **Getting paid by skew.** The side each vault will pay to have taken is [readable on-chain](price-formula.md). Makers who trade with the skew earn a spread for pushing vault inventory towards target, which is precisely the rebalancing the system is built around.
3. **No venue charges, no queue position.** Winning needs only a signature, and a losing quote costs nothing.

## Containing failures

Everything that protects the vault path applies here too:

* no faulty quote can do harm beyond the band;
* attestations control who is allowed to quote;
* expiries cap the lifetime of a stale price;
* because settlement is atomic, one failed transfer cancels the whole fill.

A compromised or hostile maker can at most fill trades within the band, which is no more than an aggressive honest maker could already do.

For onboarding, tooling and the complete quote protocol spec, see [Quote as a maker](../walkthroughs/quote-as-a-maker.md) and [API and SDK for builders](../architecture/api-and-sdk.md).
