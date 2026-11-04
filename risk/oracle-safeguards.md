---
description: Where Canopic sources its prices, and how a closed market or an unreliable feed changes trading.
---

# Oracle safeguards

Canopic accepts prices from exactly one place: the `OracleAdapter` contract. It wraps Chainlink and enforces the protocol's guards before any value can reach a quote, a fill check or the market hours logic. The anchor price underpins the entire venue, so no page in these docs matters more than this one.

## Guards applied to every price

`OracleAdapter` evaluates each of these conditions before it accepts a value. If one is met, the result is either a revert or a halt:

| Condition | Result |
| --- | --- |
| Price is zero or negative | Revert |
| Age of `updatedAt` exceeds what the current session permits | `HALTED`; trading resumes with the next round |
| A single round shows a move above 25% | `HALTED`; trading resumes once a timelocked review is published |
| Feed reports `oraclePaused()` | `HALTED` while the corporate action runs (see [Corporate actions](../trading/corporate-actions.md)) |
| The gap between the stream price and the feed price exceeds tolerance | Revert on that fill only |

Halts are local. Quoting and settlement stop in the affected market, while reading state, cancelling and withdrawing all remain available.

## Data sources

| Source | Role at Canopic | Behaviour |
| --- | --- | --- |
| **Data Feeds** from Chainlink, 24/5 equity coverage | The reference mid: vault quotes centre on it and the band is computed from it | Chainlink pushes an on-chain update when the deviation threshold or the heartbeat is reached |
| **Data Streams** from Chainlink, using the v11 RWA schema | Sub-second session classification and RFQ fill cross-checks | Pulled on demand; a closed market shows `marketStatus` equal to 5 |
| The Chainlink **Sequencer Uptime Feed** | Controls the post-outage grace window | Explained in [Chain outages and sequencer faults](chain-incidents.md) |
| ERC-8056 `uiMultiplier()` | Allows the Ledger to show balances in share terms | Taken directly from the token; Chainlink prices have it built in |

Using both feeds and streams means every price is checked against an independent source. No fill settles if it sits beyond a configured tolerance from the second source, and the stream's market status can only ever make pricing stricter than the feed would on its own.

## Sessions and what they change

At any moment a market is in a **regular**, **extended** or **closed** session. `OracleAdapter` picks the label from two inputs: the stream's `marketStatus` and how old the feed's `updatedAt` value is. That label then sets clips and spreads through the [market hours logic](../trading/market-hours.md).

| Session | Staleness limit | Effect on pricing |
| --- | --- | --- |
| Regular | 1 hour | Published tier spreads apply |
| Extended | 2 hours | Spread ×1.5, clip ×0.75 |
| Closed | 4 days | Spread ×3.0, clip ×0.5; at weekends the anchor is Friday's last close |

The closed-session multiplier reflects how far prices can travel from Friday's close to Monday's open. LPs quoting over the weekend are paid for carrying that gap, and they are paid in plain sight. Someone trading at 3 a.m. on a Sunday sees a stated premium for a genuinely risky service, rather than a hidden charge on a service priced as if it were safe.

## Pricing the held token only

Each market's `OracleAdapter` configuration prices precisely the token its vault contains. No price is ever inferred through a wrapper, a vault share, or an exchange rate between two tokens. Consider what went wrong with wGOOGLx at Edel Finance in July 2026: the underlying's oracle stayed accurate the whole time, yet the figure being relied on was a wrapper rate pushed up 78 times. [Lessons from earlier tokenized venues](lessons.md) walks through it in full.

## Replacing a market's oracle

Oracle adapters are held in `SettingsTimelock` per market, which means swapping one requires a timelocked action plus a published rationale. `OracleAdapter` exposes, per market, the current feed, session, staleness, multiplier and pause flag, and the [Ledger](../walkthroughs/use-the-ledger.md) shows all of it in real time. Every check the router applies is visible to the public as well.
