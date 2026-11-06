---
description: Where the venue stands today, which caps are in force, and the sequence of stages ahead.
---

# Milestones

Canopic grows one stage at a time. Advancing to the next stage requires hitting a published gate, not a calendar date. Features are added and caps lifted as the record justifies it. Any date we mention is a target only; what we stand behind is the gate.

## Phase 0 · Testnet (complete)

We exercised the whole stack on Robinhood Chain testnet, with Robinhood's testnet Stock Tokens and faucet supplying the assets: vaults, RFQ, the router, eligibility and the Ledger. Testnet remains the staging environment, and every release passes through it before mainnet. See [Deployments and versions](../architecture/deployments.md).

## Phase 1 · Capped mainnet (current)

Live today:

* Tier A, in five USDG markets, one per underlying: SPY, QQQ, AAPL, MSFT, NVDA.
* Market vaults running launch parameters, kept within the per-market TVL and daily volume caps listed in [Listing policy](../assets/listing-policy.md).
* RFQ block trades, quoted by the founding maker group. Attestations are open to traders, LPs and makers alike.
* Public data from day one: the Ledger, plus the dataset behind our [execution quality reports](../transparency/execution-quality.md).

**Gate to Phase 2:** all three of the following.

1. Two audits published with clean results.
2. A 60-day record of execution-quality data in which the median fill during `OPEN` meets the target for its tier.
3. A clean invariant record, with zero events on either the band or withdrawals.

## Phase 2 · Broader listings, larger caps

* Tier B and Tier C Stock Token markets, added in batches once feeds are live and bytecode reviews are complete. Serving the long tail is why the venue exists.
* Cap increases via the timelock, each one citing the record built up to that point.
* LP access opened beyond the founding group, jurisdiction by jurisdiction as the [regulatory](../compliance/regulatory-stance.md) work for each concludes.
* Completed Holdings views: cost basis, share-denominated amounts, LP analytics and CSV export.

**Gate to Phase 3:** in every tier, the vaults must hold two-sided quotes on their own. That has to survive a long weekend and a week of high volatility (at least one of each), and nobody may need to step in by hand.

## Phase 3 · Wider reach for builders and assets

* The public [API and SDK](../architecture/api-and-sdk.md) leaving beta, along with aggregator and wallet integrations. Since the chain is moving toward agents, we treat agentic order flow as an integrator of full standing.
* Our first assets from other chains, in the order set by [Next asset classes](../assets/next-asset-classes.md). Treasuries come before gold; both arrive over CCIP and must clear every item of the listing checklist.
* Algorithms that split a large order and execute it gradually: TWAP, routed through the vault path.

## Phase 4 · Governance takes over

The foundation multisig hands parameter control to the governance module. Nothing about the timelock, the requirement to publish a rationale, or the [prohibitions](../transparency/settings-changes.md) changes in the process. Who proposes changes is different; the limits on any proposal stay fixed.

## Out of scope by choice

Canopic will not offer leverage, margin, perpetuals or lending. Adjacent products are left to other protocols. The goal is to be the best place to trade RWAs, and every phase here serves only that goal.
