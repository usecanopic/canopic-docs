---
description: The concrete commitments Canopic makes, each paired with a method anyone can use to verify it.
---

# Our guarantees

Claiming transparency costs nothing. A commitment carries weight only when it can be tested, so every guarantee on this page is paired with a verification method. If a guarantee fails its test, we treat that as an incident and respond accordingly.

## Deliberately left out

Three kinds of promise are missing on purpose. We do not claim to beat all other markets on price, we do not say any position will make money, and we do not commit to availability for non-essential conveniences. We commit only to what the design can genuinely enforce, and that is precisely why these particular guarantees made the list.

## Pricing and fills

| Guarantee | Verification |
| --- | --- |
| Each fill is itemised on-chain in its fill event: mid, spread, skew and fee. | Look up a fill on Blockscout, then reconcile each component with what the receipt showed. |
| Execution beyond the oracle band is impossible. The contracts enforce this as an invariant; it is not a discretionary policy. | Review the published invariant tests, then see the margin to the band edge that the Ledger records for each fill. |
| Execution quality data is released in full with nothing filtered out: the gap between each fill and the oracle mid, summarised daily and available as raw downloads. | Consult [Execution quality reports](execution-quality.md); anyone holding the event logs can reproduce each number. |
| Canopic neither sells order flow nor favours any liquidity source in routing. Each trade goes to whichever source offers the best price the router can verify on-chain. | Using the oracle round and vault state captured in a fill's event, determine for yourself which source was cheaper. |

## Who holds the money

| Guarantee | Verification |
| --- | --- |
| Canopic takes custody of nothing. Trades settle atomically, and vault inventory is owned by the LPs. | Audits confirm the only contract functions able to move user assets are settlement and withdrawal. |
| Exits carry no conditions, regardless of vault state and regardless of any pause. | Execute the invariant tests on your own machine, or look through halt periods on the Ledger, which lists withdrawals made during a stoppage next to ordinary ones. |

## How the system can change

| Guarantee | Verification |
| --- | --- |
| No parameter changes without passing through a timelock, and the reasoning is made public ahead of execution. | The **Change history** on the Ledger records, for every change since launch, the proposal, its reasoning, the waiting window and the execution. |
| Fees never apply backwards in time. You pay whatever fee was in effect at the moment of your fill. | The applicable fee is written into each fill event. |
| A deployed settlement contract is never modified. Any new logic ships as a separate deployment that users choose to adopt. | Bytecode is matched to tagged source in CI, and anyone can reproduce the build. |

## Publishing what we know

| Guarantee | Verification |
| --- | --- |
| Before a market opens, its listing goes out with supporting evidence: the data behind its tier, the bytecode review findings and the issuer disclosures. | Each market's listing record on the Ledger holds this material. |
| After any incident, a written post-mortem is released within 14 days. | Check the incident log. A missing post-mortem is a breach in itself. |
| Risks are described without softening in these docs. Everyone who reads the core pages meets issuer, gap and oracle risk up front; none of it is relegated to an appendix. | See the [Risk framework](../risk/framework.md). |
