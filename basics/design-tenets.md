---
description: The eight principles every Canopic feature must satisfy before it ships.
---

# Design tenets

Every Canopic feature has to agree with the eight tenets on this page before it reaches users. We sort them into three groups according to what each one safeguards: pricing, users, and the platform overall.

## Tenets that safeguard pricing

### Price discovery happens off-venue

An RWA's true price is found on the market where the underlying trades, and Canopic does not pretend to find it elsewhere. The Chainlink mid is the origin point for every price we show, and no fill may stray past a limit defined around it. Our published [execution quality reports](../transparency/execution-quality.md) use that same mid as the benchmark. We do not decide what Apple is worth; we carry that value on-chain at 3am and are honest about what that service costs.

### Every quote is itemised

You will find mid, spread and protocol fee as separate labelled lines on every quote. The session badge sits directly on the ticket rather than behind a tooltip, so traders see "closed session, spreads widened" while deciding, not afterwards during reconciliation. We do not sell order flow, mark up prices or bury costs in a curve. Any revenue model that depends on hiding its components is one we will not operate.

### Market hours feed into price

Holding an equity token on a Tuesday afternoon is a different risk from holding it at 3am on Sunday, and Canopic prices that gap openly rather than ignoring it. The current [pricing mode](../trading/market-hours.md) sets the spread, the permitted clip and whether quoting happens at all. That mode is read from the oracle's market-status field; no person chooses it.

## Tenets that safeguard users

### Users keep their own assets

Canopic holds no user property at any point. Private keys never leave the trader, and only the trader can sign. LPs own the vault inventory, which sits in immutable contracts. RFQ fills move assets straight from maker to taker. An emergency pause exists, yet all it does is halt quoting: it cannot move funds or prevent LP withdrawals.

### Eligibility is verified at the door

Stock Token contracts place no limits on who can hold them, so any venue listing them must handle that question on its own. Canopic does so through attestations at every entry point, keeps identity data off-chain entirely, and explains to an ineligible user why they are blocked before any signature is requested, sparing them wasted gas. See [Regulatory stance](../compliance/regulatory-stance.md) for specifics.

## Tenets that safeguard the platform

### Each market carries its own risk

Every listed asset has its own vault, with its own LPs, parameters and inventory. A dislocation, suspension or delisting in one market is contained there. Canopic never pools liquidity across markets and never spreads one market's losses over others.

### Everything is verifiable

From any fill, a single click takes you to the matching Blockscout transaction. Any wallet or browser can read every parameter straight from public contracts. Before a setting can change, it must clear a timelock, and a written rationale is published alongside it. The [ledger](../walkthroughs/use-the-ledger.md) exists so that doubters can verify the "transparently on-chain" claim themselves rather than relying on us.

### Plan for the chain to break

A lone sequencer may halt while external markets continue. Oracle feeds may fall silent. A corporate event such as a split or dividend can leave a market without a usable price source for a while. The [Risk framework](../risk/framework.md) section spells out the response to each case (halt, widen, or hold through a grace period), since infrastructure that works only when everything else works does not deserve the name.

## How the tenets shape the product

| Commitment | Result |
| --- | --- |
| Liquidity is self-funding | No emissions exist. LPs are rewarded from spread the venue genuinely earns, and from nothing else. |
| A single purpose | Canopic is spot trading only. Borrowing, margin and liquidation are not part of it. |
| Prices are never set by hand | No one steps in to choose a price. Quotes come from the formula, and parameters move only via the timelock. |
