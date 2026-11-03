---
description: What an asset has to satisfy before trading starts, and how tiers translate into market settings.
---

# Listing policy

Canopic lists assets against a fixed checklist, not on judgement calls. Qualification for a tier is a yes or no answer. The evidence is published with each listing, and after the tier is assigned the market's parameters follow mechanically, with no room for discretion.

## Requirements for every market

These apply in every tier. No market opens until each one is satisfied:

1. **Token-specific Chainlink pricing on Robinhood Chain.** The Data Feed has to be live for the precise token. Vaults are priced on what they actually contain; wrappers, and rates computed from wrappers, are not accepted.
2. **Session data via Data Streams market status, or an equivalent signal.** [Market hours and pricing modes](../trading/market-hours.md) depend on an authoritative session source.
3. **Real backing from a regulated issuer.** Terms must be public and every token must be matched one to one by the asset. Synthetic exposure does not qualify.
4. **Bytecode review of the live contract.** We look for pause, freeze, blacklist and forced-transfer roles ahead of launch so there are no surprises afterwards, and we publish what we find.
5. **Plain ERC-20 mechanics.** Transfers carry no fee, and the token does not rebase, except through ERC-8056-style multipliers already captured by the feed.

An asset that misses even one requirement receives no tier. Nobody can override this.

## Tier parameters

Settings are set per tier, and every market takes those of its own. At launch they are:

| Parameter | Tier A | Tier B | Tier C |
| --- | --- | --- | --- |
| Half-spread at launch | 10 bps | 20 bps | 40 bps |
| Maximum inventory skew | 15 bps | 25 bps | 50 bps |
| Oracle deviation band | 75 bps | 150 bps | 300 bps |
| Maximum vault fill size, regular session | 50,000 USDG | 20,000 USDG | 5,000 USDG |
| Daily volume cap, launch period | 2M USDG | 750K USDG | 200K USDG |
| Vault TVL cap, launch period | 5M USDG | 2M USDG | 500K USDG |

### Tier membership

| Tier | Usual assets |
| --- | --- |
| A | Mega-cap stocks and major index funds (SPY, QQQ, AAPL, MSFT, NVDA), with deep primary-market liquidity and frequently updated feeds |
| B | Liquid individual stocks that fall short of mega-cap |
| C | The remaining long tail |

A word on Tier C. Assets with wider spreads on their home market and slower feeds are still welcome. Their wider band and spread account for those traits, since giving the long tail good execution is a core reason Canopic exists. The caps keep the resulting risk in proportion.

### Assigning a tier

Three measurements decide a tier:

* Underlying liquidity, judged by average daily volume and the normal spread on the primary exchange.
* Feed update frequency.
* On-chain distribution, meaning how many addresses the token is spread across.

Every market's tier is published along with its supporting evidence. Changing a market's tier goes through the timelock like any other parameter change.

## Delisting

When an asset no longer passes the requirements, its market is retired. Typical triggers are a feed being withdrawn, the issuer winding down the token, or a bytecode change that adds a disqualifying control. Retirement is executed through the timelock with a public rationale, after which the vault only processes withdrawals. Read [Corporate actions](../trading/corporate-actions.md) for more.

## Other asset types

There is one checklist for everything, including tokenized treasuries and gold; each of those adds a single condition of its own. Assets priced by NAV need a feed that tracks NAV, and they trade within a tight band around it. Bridged assets also need the bridge review set out in [Next asset classes](next-asset-classes.md).
