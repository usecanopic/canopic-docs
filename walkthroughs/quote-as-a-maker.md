---
description: "A guide for professional market makers covering admission, the life of a quote from request through settlement, and the opportunity in vault skew."
---

# Quote as a maker

Professional liquidity on Canopic is supplied by market makers. They absorb block orders via [RFQ](../trading/large-orders.md), beat the vaults on price whenever their own pricing is sharper, and earn the skew term in [the price formula](../trading/price-formula.md) for steering vault inventory back to target. Quotes that lose cost nothing. Quotes that win settle atomically in one step.

## Getting admitted

To receive requests you need a `MAKER` attestation, which professional trading firms obtain through the standard verification process. The **Access** section of the **Account** page lists it as the role that lets you answer requests with signed quotes. Launch terms are minimal: there is no quoting obligation, no membership fee and no volume threshold. Reliable quoting earns priority in request routing, and that is the only distinction between makers.

### What you need technically

* A signing key for EIP-712 messages, which may be an EOA or an EIP-1271 smart account.
* A Permit2 allowance to `RfqSettler`, left in place on your settlement wallet.
* USDG inventory, plus inventory in each token you plan to quote.
* Live connectivity to the gateway that serves makers.

## Life of a quote

1. **Request arrives.** Once you are connected to the gateway over WebSocket (see the [API and SDK for builders](../architecture/api-and-sdk.md) for the spec), each broadcast tells you the pair, the size, the side and the taker's attestation class.
2. **You price it.** With US exchanges trading (`OPEN`), a hedge can be placed almost immediately. During `CLOSED`, your price is a deliberate bet on how far the market may gap over the weekend. The vault is quoting widest at exactly that moment, which is when a sharper price from you counts the most.
3. **You answer.** A signed price must reach the gateway within the collection window of a few hundred milliseconds, and the best one wins. At settlement the chain verifies five conditions: a valid signature, a live attestation, a fresh nonce, an unexpired quote, and a price sitting within the oracle band.
4. **It settles.** A single atomic step moves `tokenOut` out of your wallet and delivers `tokenIn` to you. If there is no fill, no tokens move at all.

Nonces let you cancel many quotes at once on-chain, and should the sequencer censor you, the L1 delayed inbox still gives you a route to cancel. Keep expiries short, a few seconds at most. A stale quote will hit the band and revert instead of executing at a bad price, but managing your hedge assumptions remains your responsibility.

## The skew opportunity

Vault skew is public information. Calling `OracleVault.quote()` shows, for every vault, which side it is currently sweetening to draw in rebalancing flow, up to its maximum skew term at the edge of the band. Trading opposite a skewed vault, within the clip, captures spread against the oracle mid without taking on risk. This is the rebalancing mechanism behaving exactly as designed, not an exploit. Makers who do this routinely keep the vaults two-sided and are paid the skew in return.

## Checks the contracts run on you

* **Signature, nonce, expiry.** Standard verification that rules out any replay.
* **A valid `MAKER` attestation when the fill happens.** Once it is revoked, your quotes can no longer settle.
* **The oracle band, enforced per fill.** A price beyond it can neither fill you nor be filled by you.
* **Atomicity.** Should either side's transfer fail, the whole fill unwinds.

That symmetry is intentional. It limits what a compromised maker key can extract from takers, and it limits what a stale quote can cost you.

## Tracking how you perform

Your fills appear among all the others on [the Ledger](use-the-ledger.md), where makers are listed as anonymous unless they opt in to being named. Authenticated API endpoints return your personal fill history and quote statistics, including how often you win and how much you improve on the vault price. That improvement figure is what sets routing priority.
