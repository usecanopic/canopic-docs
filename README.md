---
description: Canopic is a non-custodial marketplace on Robinhood Chain for tokenized stocks and other real-world assets. Its prices, executions and charges are all public on-chain.
---

# Canopic in brief

Canopic lets you trade tokenized real-world assets on Robinhood Chain without giving custody of anything to a third party. At launch, the venue pairs Stock Tokens (tokenized AAPL, NVDA, SPY and others on the list) against USDG, with tokenized treasuries and gold planned for later. Pricing draws on the Chainlink feeds the broader market already trusts. Your quote, your executed trade and the fees you paid are all recorded on Robinhood Chain, so anyone can verify them.

## How it works, in one paragraph

Most AMMs derive a price from the reserves sitting in the pool. That approach suits tokens whose price is discovered on-chain, but it fails for assets priced on an external exchange. Canopic works the opposite way. Each market is backed by a [market vault](trading/market-vaults.md) that begins at the Chainlink mid and layers on a spread made of three components: the current session of the underlying market, the vault's inventory balance, and the asset's tier. When the home market closes, spreads widen and the maximum trade size shrinks, and the ticket shows you both adjustments. Anything larger than the vault's clip is handled by [RFQ](trading/large-orders.md), where professional makers sign quotes off-chain and settlement happens on-chain as a single atomic transaction. The [router](trading/routing.md) prices both routes, picks whichever is cheaper and shows the trader one number, itemised into mid, spread and fee.

## How Canopic differs from a standard AMM

| Topic | Conventional AMM pool | Canopic |
| --- | --- | --- |
| Price source | Implied by the pool's reserve ratio | The Chainlink mid, bounded by guards that limit deviation |
| Outside market hours | Weekend quotes look the same as weekday ones | Spreads widen and clips shrink, and the ticket says so |
| Corporate events (splits, dividends) | Pricing breaks, often forcing a fresh pool deployment | Applied to the price via the ERC-8056 multiplier, with trading paused during the update |
| LP returns | Arbitrage bleeds value on every price move | Spread earned on oracle-centred quotes |
| Large trades | Slippage from moving along the curve | Attested makers bid for the order through RFQ |
| Compliance | None | Checked at every protocol entry point |
| Fee visibility | Hidden inside the curve | Itemised on the quote and logged on-chain |

Constant-product curves work well for pairs that exist only on-chain. Apply one to an asset with an outside reference price, fixed trading hours and corporate actions, and the results are predictable: LPs leak value to arbitrage, and prices drift over the quiet weekend. Such assets call for purpose-built market structure, which is exactly what Canopic provides.

## Key facts

| Area | Specifics |
| --- | --- |
| Chain | Robinhood Chain (an L2 on the Arbitrum Nitro stack) |
| Quote currency | USDG, the Paxos Global Dollar |
| Launch markets | Stock Tokens issued by Robinhood |
| Later markets | More of the long tail of Stock Tokens, followed by tokenized treasuries and gold (sequence described in [Next asset classes](assets/next-asset-classes.md)) |
| Execution | Vaults anchored to the oracle for everyday size, RFQ for blocks, and one router in front of both |
| Pricing data | Chainlink Data Feeds and Data Streams behind guards, adjusted for the market session |
| Custody | None. Tokens move only at settlement, and each vault's inventory sits in immutable contracts |
| Fees | One protocol fee, itemised; no hidden take from the spread |

## Out of scope

* Taking custody of customer funds, or acting as a fiat on-ramp or off-ramp.
* Issuing RWAs, or managing their mint and redeem process. That foundation belongs to regulated issuers, and Canopic operates the secondary market on top of it.
* Lending, margin trading or perpetual futures in any form.

## Pick a starting point

* **First visit:** begin with [Why this venue, and why now](basics/why-now.md), followed by [Design tenets](basics/design-tenets.md).
* **Curious about trading mechanics:** see [The trade path, end to end](trading/overview.md), then [Market vaults](trading/market-vaults.md), [The price formula](trading/price-formula.md) and [Market hours and pricing modes](trading/market-hours.md).
* **Integrating with Canopic:** open the [Architecture overview](architecture/overview.md), [On-chain components](architecture/onchain-components.md) and [API and SDK for builders](architecture/api-and-sdk.md).
* **Assessing risk:** review the [Risk framework](risk/framework.md) and [Lessons from earlier tokenized venues](risk/lessons.md).
* **Ready to act:** follow [Place your first trade](walkthroughs/place-a-trade.md) or [Fund a vault](walkthroughs/fund-a-vault.md).
