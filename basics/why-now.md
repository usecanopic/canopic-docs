---
description: The 2026 market shifts that made a dedicated venue for tokenized real-world assets worth building.
---

# Why this venue, and why now

Three conditions have to line up before a venue like Canopic makes sense. Tokenized equities must act like ordinary on-chain assets with trustworthy prices. People must actually want to trade them. And today's venues must be leaving a meaningful gap in how that trading happens. In 2026, every one of these conditions is met.

## Condition 1: the asset class has grown up

Excluding stablecoins, transferable RWAs on public chains grew from about $7.9B at the end of 2024 to about $21B at the start of 2026, and stand near $38.7B today. Tokenized equities are the fastest-compounding category inside that figure.

| Category (rwa.xyz data, 28 Aug 2026) | On-chain transferable value | Notes |
| --- | --- | --- |
| Stablecoins | ~$303B | Ultimately, everything settles here |
| Tokenized US Treasuries | ~$16.0B | Biggest products include USYC ($2.9B), BUIDL ($2.8B), BENJI (~$2.4B) and USDY ($2.2B) |
| Private credit, tokenized | ~$7.5B distributed | Adding non-transferable "represented" assets (Figure HELOCs, for instance) lifts this to about $35B |
| Commodities | ~$3.1B | XAUT, PAXG |
| **Equities** | **~$2.6B** | Robinhood, Coinbase and Binance among the issuers and venues; Ondo Stocks above $1B TVL; xStocks past $25B in cumulative volume |
| Private equity and venture funds | ~$1.6B | |
| Property | ~$175M | |

In the equity segment alone, value went from near $424M (mid-2025) to near $2.59B (August 2026). Different data providers report figures about 20 percent apart, largely because they differ on whether non-transferable assets are included.

## Condition 2: people want to trade, and trading is where things break

Issuance works. Secondary trading does not. Most volume in tokenized equities runs through centralised order books, so it is custodial, off-chain and opaque to outsiders. On-chain, traders are left with AMMs built for crypto-native pairs, and those AMMs fail on RWAs in ways anyone could foresee:

| Problem | Effect |
| --- | --- |
| Prices drift with the reserve ratio | A constant-product pool has no view of fair value. On thin books, tokenized equities have printed well above the underlying; [Lessons from earlier tokenized venues](../risk/lessons.md) records the dislocations seen in the 2025 launch weeks. |
| LPs get arbitraged | Each move on the home exchange makes the curve stale, and arbitrageurs capture the gap before it updates. Passive curve liquidity for an externally priced asset amounts to a permanent subsidy for the fastest trader. |
| Closed hours lack a real price | Regular trading adds up to about 32 hours a week. Treating a Saturday like a Tuesday misprices gap risk across roughly 80 percent of the calendar. |
| Corporate actions break the pool | A split or reverse split changes the token price overnight, and a pool unaware of it gives the difference away to the first trader to arrive. |

Issuer mint and redeem offers an exit, not a market. To date, no sizeable on-chain venue has treated these assets as what they are: their price is set elsewhere, they follow exchange hours, and corporate actions change them.

## Condition 3: the infrastructure has arrived

Robinhood Chain went live on mainnet on 1 July 2026. Several ingredients meet there and on no other L2: plain ERC-20 equity tokens, backed 1:1 and issued by a regulated broker; Chainlink pricing on a 24/5 schedule; Data Streams with a market-status field; fees under one cent per transaction; ERC-4337 account abstraction; and open, permissionless deployment. Assets, prices and users are all in place. What has been missing is a venue designed around the way these assets really behave. The chain itself is covered in [Building on Robinhood Chain](../chain/robinhood-chain.md).

## The gap other venues leave

* **Kraken's xStocks, Bybit and Binance:** activity concentrates on their centralised order books, which keeps it custodial and off-chain, the very thing tokenization set out to replace.
* **Ondo Global Markets:** a proprietary broker link connects its orders with liquidity on US exchanges. That gives depth, at the cost of dependence on one issuer and on the US session.
* **Uniswap v4:** since July 2026 it supports permissioned pools. Those settle who is allowed to trade, while leaving RWA pricing unaddressed.

A venue that is non-custodial, oracle-anchored and session-aware takes nothing away from what these players do well. Canopic complements them by covering the ground they leave uncovered. The principles that shape it are described in [Design tenets](design-tenets.md).
