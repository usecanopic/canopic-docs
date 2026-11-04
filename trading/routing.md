---
description: How one on-chain contract compares vault and maker prices and fills at the better of the two.
---

# How the router picks a venue

## One contract for every trade

All trading flows through a single contract, `SwapRouter`, which has four responsibilities: checking that the caller is eligible, deciding where each fill takes place, building trades that span multiple legs, and enforcing the limits that apply to every fill.

There are no restrictions on who may call it. The router is a public contract with a stable interface, so eligibility is tied to the address making the call, not to the front-end that submitted it. Any wallet, portfolio product or agent is free to price trades with the [quote API and SDK](../architecture/api-and-sdk.md) and call the contract itself. The reference app at [usecanopic.com/platform](https://usecanopic.com/platform) has no special access of any kind.

## Choosing between vault and maker

For any given size, two sources compete. The first is the market vault, which is eligible only if the size is within its clip and the current market hours mode allows quoting. Second come maker quotes, provided they are valid and attached to the call. Whichever source gives the trader more is the one the router settles against.

The choice happens on-chain and is entirely mechanical:

* The router does not accept a vault price from a front-end at face value. Oracle and vault state are read afresh within the transaction to derive it independently.
* A maker quote counts only after its signature has been verified.

As a result, a front-end has no way to push an order towards a worse price. Submit a maker route that is worse than the vault and the router simply rejects it.

## Token-to-token trades

A trade from one token to another becomes two legs routed through USDG and settled together:

| Leg | Action | Possible fillers |
| --- | --- | --- |
| First | Sell `tokenIn` for USDG | That market's vault, or a maker |
| Second | Use the first leg's USDG to buy `tokenOut` | That market's vault, or a maker |

Each leg picks its venue on its own, so a vault can fill one leg and a maker the other. Both legs have to clear the band and mode checks of their respective markets, and if either one fails the full trade is reversed. The fill event records the two legs with an itemised price breakdown, and the trader's signed slippage limit applies to the overall rate across both.

## Checks on every fill

| Check | What it prevents |
| --- | --- |
| `TRADER` attestation on the calling address | Trading by ineligible addresses |
| Oracle band, applied per leg | Fills far from the guarded oracle price |
| Market hours mode must allow quoting | Fills in a halted market |
| Clip and daily volume limits | Exceeding guarded-launch volume limits |
| Trader's slippage limit and deadline | Execution on stale conditions |
| Permit2 pulls exactly equal to the signed amounts | Using granted approvals for anything beyond this trade |

## Ordering and MEV

Robinhood Chain sequences in strict arrival order, with preconfirmations coming back after roughly 100 ms. That rules out the public-mempool sandwiching common on Ethereum at present.

Canopic does not assume this will last. Every fill is already bounded by the signed slippage limit and the oracle band, so anyone with an ordering edge can take at most the tolerance the trader accepted beforehand. If the chain enables Timeboost or otherwise changes how it orders transactions, the review that follows is recorded in the governance log. See [Chain outages and sequencer faults](../risk/chain-incidents.md) for more.
