---
description: How each market's pricing mode is determined, and how that mode changes the way the market is quoted.
---

# Market hours and pricing modes

Stock Tokens trade around the clock, while regular equity sessions account for only about 32 hours a week. Canopic does not pretend that gap away. The regime engine charges for it explicitly.

## The four modes

| Mode | When it applies | Effect on quoting |
| --- | --- | --- |
| **`OPEN`** | Oracle market status reports regular trading | Tightest spreads, full-size clips |
| **`EXTENDED`** | Oracle status reports extended trading hours | Spread × 1.5, clip × 0.75 |
| **`CLOSED`** | The underlying market is not trading | Spread × 3.0, clip × 0.5 |
| **`HALTED`** | Any one of five triggers, listed under Halts below | No quotes at all, though deposits and withdrawals continue |

Each market's mode is held individually in `OracleAdapter`, and every quote consults it. The multipliers are per-tier `SettingsTimelock` values.

## What decides the mode

`OracleAdapter` sets the mode from two independent signals:

1. `marketStatus`, a value delivered through Chainlink Data Streams. Under schema v11 for RWAs, a reading of 5 signals closed.
2. `updatedAt` on the Data Feed, measured against a staleness limit that depends on the session: 1 hour for regular trading, 2 hours for extended trading, and a longer allowance when the market is shut.

Each signal can only make the mode stricter, never looser. A fresh feed paired with a closed status from the stream still yields a closed market.

The transition function is open to any caller. In practice the keeper service fires it seconds after each session change. Should a swap get there first, it derives the correct mode on its own as part of that transaction. Pricing from an earlier session therefore never carries over, not even briefly.

## Why markets still quote when `CLOSED`

Both alternatives are worse:

* **Going dark at weekends** imposes exchange hours on a chain that runs continuously, and drives users to custodial venues.
* **Using weekday spreads at weekends** turns LPs into the uncompensated counterparty to every Monday opening gap.

The sensible position is in between. Weekend liquidity exists, but it is pricier and smaller, and the ticket spells out the extra charge. Gap risk is exactly what the closed-session multiplier charges for. It launches on the conservative side, gets recalibrated against real Monday opens, and can only be changed via the timelock.

## Halts

A market stays halted for as long as any of the following holds, and resumes quoting the moment every one has cleared:

| Trigger | What clears it |
| --- | --- |
| Feed age exceeds the staleness bound for the session | A new oracle round |
| The feed signals `oraclePaused()` during a corporate action | The feed restarting (see [Corporate actions](corporate-actions.md)) |
| Price jump above the 25% guard within one round | A review through the timelock, with the results made public |
| The one-hour grace window following a sequencer outage has not yet ended | The window expiring (see [Chain outages and sequencer faults](../risk/chain-incidents.md)) |
| Manual pause by a guardian | Lifting the pause, with a published incident note |

A halt stops quoting and nothing more. LPs keep the ability to deposit and withdraw, RFQ quotes remain cancellable, and all view functions continue to answer. The Ledger shows the live halt reason, read from the same contract state the router uses.

## Calendars stay out of the code

You will not find a market calendar written into the contracts. Holidays, early closes and unscheduled shutdowns are all signalled by the oracle's market status. So the venue will not quote into a market the data reports as closed, and will not sit idle while the data reports it open.
