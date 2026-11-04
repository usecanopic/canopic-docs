---
description: The full list of protocol charges, who pays each one, and where the income goes.
---

# Fees and spreads

Canopic charges only at the points where value moves between parties. Every charge is public and shows up on its own line of the trade ticket. There are no hidden markups, and Canopic does not sell order flow.

## Fee schedule

| Item | Launch rate | Paid by | When |
| --- | --- | --- | --- |
| Vault swap fee | 2 bps on notional, part of the all-in price and itemised on the ticket | The trader | On every fill |
| Protocol share of vault spread | One tenth (10%) of what the vault earns in spread | LPs, deducted from spread earnings | On every fill |
| RFQ settlement fee for a maker quote | 2 bps on notional | The trader | On every RFQ fill |
| Deposits to and withdrawals from a vault | No charge | | |

A two-leg swap pays the charge on both legs.

## Changing a rate

All rates are `SettingsTimelock` values. Changing one requires going through the timelock with a published rationale, and a change can never apply retroactively. Each fill pays whatever rate was active at the moment it executed. A new rate is logged in the governance log before it can appear on any ticket.

## The quote is also your receipt

Before you sign, the ticket breaks out the mid, the spread and the fee individually, in both bps and USDG. Once the trade settles, the emitted event stores the same breakdown on-chain.

## Where fee income goes

Every fee is paid into the `ProtocolTreasury` contract. From there it pays for security work (audits and the bug bounty), oracle and infrastructure costs, and venue operations. Control over spending moves to the governance module at the same time as control over parameters. Details are in [Changing protocol settings](../transparency/settings-changes.md).

## Illustrative revenue

Assume daily volume of 10M USDG and an average all-in half-spread of 12 bps.

| Stream | Working | Annual |
| --- | --- | --- |
| Vault and RFQ trading fees | 10M × 2 bps × 365 | roughly 730K USDG |
| Protocol share of spread | 10M × 12 bps × 10% × 365 | roughly 438K USDG |
| Combined | | about 1.2M USDG |

Given how fast tokenized assets are growing, those are conservative volumes. At 50M per day, the same calculation gives about 5.8M USDG annually. The [Business model](../reference/business-model.md) page covers this in more depth.
