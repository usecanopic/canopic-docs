---
description: A guided path from connecting a wallet to a settled trade on Canopic, plus a guide to the ticket and the receipt.
---

# Place your first trade

## Before you start

| You need | How to get it |
| --- | --- |
| Somewhere to sign | Robinhood Wallet and MetaMask are both fine, as is practically any mainstream EVM wallet. If you would rather not, an embedded account secured with a passkey can be created inside the [app](https://usecanopic.com/platform) itself. |
| Clearance to trade | Go through attestation a single time. Identity, sanctions screening and jurisdiction are all verified by a KYC provider, which on success records a `TRADER` attestation against your address. Your personal details never touch the chain, and the **Access** section of the **Account** page always shows which roles your wallet holds. See [Eligibility checks](../architecture/eligibility.md) for the full mechanism. |
| Something to pay with | Every listing is priced in USDG, so that is the simplest starting balance. Stock Tokens you already own work too: going from one token to another routes through USDG on its own, and you do nothing extra. |

## Understanding the ticket

Open **Trade** from the sidebar, pick a market under **Listing**, type an amount into **You send**, and the ticket fills in **You get** along with a line by line breakdown:

* **Oracle mid.** The Chainlink price, after it has passed the guards, that everything else is built on. The oracle round it was taken from is shown beside it.
* **Half-spread.** Expressed twice (bps, then USDG), this is the venue's present price for liquidity; inventory skew is baked into it.
* **Venue fee.** What the protocol itself takes, listed separately from the spread.
* **Regime badge.** Displays the session state (`OPEN`, `EXTENDED` or `CLOSED`) next to the spread multiplier it carries.

A `CLOSED` badge means weekend liquidity costs about three times the normal spread and the size you can trade is reduced. That markup reflects the real cost of trading an asset while its primary exchange is not running. In effect, the ticket is pointing out that if your trade can hold until Monday's open, it will probably cost less.

## Built-in safeguards

You choose a **Price tolerance** on the ticket, and the line **Least you will get** turns it into a hard floor. Each transaction you sign carries that minimum output along with a deadline. If the price or the vault moves beyond your floor before inclusion, the transaction reverts instead of settling. Preconfirmations arrive in about 100 ms, so this is rare in practice. RFQ quotes also expire within seconds, and the app requests a new one for you whenever a quote runs out.

## Larger orders

Any order above the vault clip is priced through [RFQ](../trading/large-orders.md) by the router. From your side, very little changes: you see the same ticket and the same breakdown, and the only difference is that the source of the fill is a maker and not the vault. Execution algorithms do not exist yet, so if you want to work an order over time you split it yourself for now. They are scheduled in [Milestones](../roadmap/milestones.md).

## Once it settles

Each receipt is matched to the chain event emitted at settlement. You get the breakdown you were shown at quote time, the source that filled you, and a link to Blockscout. Every fill on the venue, yours included, is then compared with the oracle mid in the [execution quality reports](../transparency/execution-quality.md), so you can verify execution instead of taking it on trust. Your own fills are also listed under **Your trades** on the **Holdings** page.

## What you actually own

A Stock Token is not a share, and holding one does not make you a shareholder. It is a tokenized debt claim on Robinhood Assets (Jersey) Ltd whose value tracks the share price. Dividends do not arrive as cash; instead they accrue within the token via the ERC-8056 multiplier. Before you trade meaningful size, read [Stock Tokens](../assets/stock-tokens.md) and [Issuer exposure](../risk/issuer-exposure.md) at least once. Your first trade also shows a brief disclosure covering both.

## If a market halts

A market can halt because its oracle has gone stale, because of a corporate action, or while the venue recovers from a sequencer fault. In each case quoting stops for that one market only. Your balances remain in your own wallet throughout, and no funds are frozen. The app explains what triggered the halt and what has to happen before it lifts. [Market hours and pricing modes](../trading/market-hours.md) covers this in more depth.
