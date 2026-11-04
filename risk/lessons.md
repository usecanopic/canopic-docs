---
description: What went wrong in the first years of trading tokenized assets, mapped to the part of Canopic built to prevent it.
---

# Lessons from earlier tokenized venues

Putting settlement on a blockchain changes the mechanics of completing a trade. Market structure stays exactly as it was. From 2020 through 2026, one episode after another showed what happens when a venue trades tokenized assets while ignoring their nature. Canopic treats that history as a list of design requirements rather than an argument.

## The underlying mistakes

The incidents on this page all come down to three errors: the venue took custody of user assets, prices floated away from their reference, or a value was computed when it should have been read from a feed. Canopic's design closes off each one:

| Mistake | How Canopic answers it |
| --- | --- |
| Custody sat with the venue | **Zero custody.** Trades settle atomically; vault exits pay out in kind. |
| Prices drifted from their reference | **All prices are anchored.** The guarded Chainlink mid bounds every fill. |
| Values were computed, not fed | **Nothing is derived.** Markets exist only for the precise token that has a feed of its own. |

Issuer, gap, sequencer and contract risk all remain, and all are real. The rest of this section covers each of them in turn, together with the mechanisms that contain them.

## Case studies

### FTX and CM-Equity tokenized stocks (2020 to 2022)

* **What happened.** FTX sold tokenized stocks through a custodial structure with CM-Equity. After FTX collapsed in November 2022, holders found they owned a claim in bankruptcy, and the structure behind the tokens had never been made clear to them.
* **Lesson.** Custody is where the risk lives. If an exchange holds your tokenized share, your share inherits the exchange's solvency.
* **How Canopic responds.** Canopic custodies nothing. Users keep assets in their own wallets or in non-upgradeable vault contracts, and LPs can always exit in kind, whatever state the system is in, halts included. With nothing held, there is nothing to lose.

### Solana stock tokens trading far from fair value (July 2025)

* **What happened.** When tokenized stocks arrived on Solana, they spent their first weeks in very thin DEX pools, where prices overshot the underlying, at times by several multiples of fair value. Those pool prices depended only on reserves and referenced nothing outside the pool.
* **Lesson.** Reserve ratios have no concept of fair value. A thin pool will show whatever its formula produces.
* **How Canopic responds.** Every quote starts from the Chainlink mid, and the band makes "no fill may land beyond this distance from the reference" a rule the contracts enforce, regardless of how thin liquidity becomes.

### Curve pools for assets priced on other venues (a continuing cost)

* **What happened.** Rather than a single event, this is a steady leak. Each reference price move lets arbitrageurs extract profit from LPs in constant-product RWA pools, a cost the research names loss-versus-rebalancing. A passive curve on an asset that another exchange reprices all day keeps handing value away from its LPs.
* **Lesson.** Where price discovery already happens elsewhere, read that reference rather than rediscovering the price.
* **How Canopic responds.** Market vaults quote around the oracle, so LPs earn spread from real flow and not from what arbitrage leaves. Rebalancing still costs something, but that cost is charged visibly through skew rather than escaping unnoticed.

### Edel Finance and wGOOGLx (July 2026)

* **Loss.** Roughly $403K of bad debt.
* **What happened.** The Chainlink oracle for tokenized Google stock stayed accurate throughout. The system instead trusted a wrapper token's exchange rate, pushed to 78 times its true value, and the valuation built on top of it.
* **Lesson.** Only the vault's own token should ever be priced. Any figure derived from that price is a lever an attacker can pull.
* **How Canopic responds.** `OracleAdapter` reads the feed of the vault's own token and nothing else. No market is ever opened on a wrapper, a vault share or a derived rate.

### Gaps on round-the-clock tokens over weekends

* **What happened.** A token that trades 24/7 on an underlying open only 32 hours a week will drift while the home market sleeps, then reprice abruptly when it opens. Venues tried two fixes and neither worked. Keeping weekday spreads through Saturday and Sunday left LPs starting every Monday at a loss, and shutting the venue simply pushed weekend trading onto custodial order books.
* **Lesson.** The asset decides its trading hours; the venue's job is to price around them.
* **How Canopic responds.** When the underlying market is closed, Canopic's market hours logic raises the spread, shrinks the clip, shows both on the trade ticket, and takes halt signals from the oracle's reported market status instead of a hard-coded calendar.
