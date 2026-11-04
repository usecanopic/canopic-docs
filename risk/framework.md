---
description: Each material hazard on Canopic, the party it falls on, and the mechanisms that cap its impact.
---

# Risk framework

Putting real-world assets on a blockchain does not leave their real-world dangers behind. We think the right response is to list those dangers openly, name the people they affect, and document the mechanisms that contain them. That is the purpose of this section. If you notice a hazard that belongs on this list but is missing, tell us: we treat a gap here the same way we treat a defect in the docs.

## The hazard table

| Risk | Borne by | Mitigations | Details |
| --- | --- | --- | --- |
| Oracle failure or manipulation | LPs and traders | Halts, the band, staleness limits, cross-checks and further guards | [Oracle safeguards](oracle-safeguards.md) |
| Weekend and overnight gaps | LPs | Reduced clips and higher spread multipliers while the home market is closed | [Market hours and pricing modes](../trading/market-hours.md) |
| Issuer credit and freeze powers | Every Stock Token holder | Bytecode review, disclosure, tiered caps, and one published single-issuer exposure figure | [Issuer exposure](issuer-exposure.md) |
| Sequencer outage or censorship | LPs, and traders with transactions in flight | Halts, a post-recovery grace window, and forced inclusion through L1 | [Chain outages and sequencer faults](chain-incidents.md) |
| Smart contract defects | Everyone | Audits, invariants, a bounty, guarded caps and as little upgradeability as possible | [Security program](../architecture/security.md) |
| Adverse or toxic flow | LPs | Skew-sensitive pricing, clips, inventory bands and one-sided quoting | [Market vaults](../trading/market-vaults.md) |
| Depeg of the quote asset | Everyone | A chain-native issuer under MiCA regulation, plus disclosed concentration | [Settling in USDG](../assets/usdg.md) |
| Misconfigured parameter | LPs and traders | The timelock, guarded caps, a written rationale for every change, and the band as a final stop | [Changing protocol settings](../transparency/settings-changes.md) |
| Maker misconduct | Takers | Expiry, nonces, signature checks, revocable attestations and the band | [Large orders and signed maker quotes](../trading/large-orders.md) |
| Regulatory change | Canopic as a venue | Jurisdiction-based access, enforced at a boundary that one parameter can narrow | [Regulatory stance](../compliance/regulatory-stance.md) |

## Why the band catches almost everything

Follow the failure paths in the table and nearly all of them end at one control, the oracle band. A bad parameter, a stolen maker key or a quote sent to the wrong place each begin earlier in the pipeline. None of them, though, can yield a fill more distant from the last guarded oracle price than the band allows. What would otherwise be an open-ended list of scenarios reduces to a capped cost per fill. The guarded launch caps rely on that cap: it turns the worst case into a figure you can compute beforehand, not one you simply trust to be modest.

## Risks Canopic leaves with you

Any honest framework also has to spell out what remains:

* **Market movement in your position.** If you hold tokenized NVDA, you carry NVDA's price risk. Canopic sets prices for trades. It does not insure holdings.
* **Issuer insolvency.** Disclosure and caps keep this exposure visible and bounded in size. Neither removes it.
* **An accurate oracle during a turbulent market.** Stale data, pauses and implausibly large jumps trigger halts. Real volatility does not. When the underlying drops hard, Canopic reflects that drop exactly as the primary listing does.

## How the table stays up to date

Any incident affecting a row in the table leads to a published post-mortem. If the findings justify it, a design or parameter change then goes through the timelock. We also track incidents at other venues and record Canopic's response to each in [Lessons from earlier tokenized venues](lessons.md), and we expect our own record to be judged by the same standard.
