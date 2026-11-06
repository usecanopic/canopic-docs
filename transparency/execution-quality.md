---
description: Every Canopic fill is scored against the reference price, and the full set of scores is public.
---

# Execution quality reports

What Canopic sells is execution, so we report on it the way an infrastructure company reports availability. Scoring is continuous, no fill is excluded, and the yardstick is one the venue has no power to adjust.

## The per-fill score

A fill's score measures, in basis points and with its sign kept, how far the achieved price sits from the guarded Chainlink mid of the oracle round named in that fill's event.

* **Above zero:** the trader paid over mid. That is normal, and the amount is the spread.
* **Below zero:** the trader received a better price than mid, which occurs when competing RFQ makers outbid the vault.

The yardstick is identical to the mid used to validate the fill. As a result, chain data alone is enough to reproduce each score precisely, and the venue has no way to substitute a kinder benchmark after the fact.

## What gets published

| Dataset | Contents |
| --- | --- |
| Live view | Rolling statistics for every market, liquidity source and regime combination, each giving the median, the 90th percentile (p90) and the worst individual fill |
| Daily file | A download of every single fill, with no size floor and no omissions |
| Regime comparison | `OPEN` and `CLOSED` costs placed next to each other, so the weekend premium is a measurement rather than a guess |
| RFQ improvement | The aggregate amount by which makers beat the concurrent vault quote |
| Revert rate | How frequently trades are rejected by either the slippage bound or the band. We publish it because turning trades away is a way to flatter the trades that remain |

On the **Ledger** page in the app, the **Cost vs mid** column and the **Average cost vs mid** figure show this same score for recent trades.

## Signs of a healthy venue

1. At the median, a normal Tier A fill during `OPEN` should cost about the fee plus half of the base spread. A sustained rise above that level means the parameters need retuning, and any timelocked adjustment will reference this data.
2. Under `CLOSED`, the spread of outcomes should be wider and more expensive, because gap risk is being charged for. Even so, when conditions are calm, nothing should be filling anywhere close to the band limit.
3. When band margins tighten abruptly across a large number of fills, you are looking at the earliest warning of an oracle fault. That is why our operations alerting watches exactly these public figures.

## Why the costly fills stay in

Once data has holes, it becomes advertising. Releasing everything, pricey weekend trades and reverts included, gives every participant the same record to work from: an LP evaluating a vault, a maker calibrating prices, a trader deciding whether a Saturday order can wait. The venue is also held to account against that record. Scoring ourselves honestly is how Canopic gets better, and the public dataset serves directly as the input for our own tuning.

Raw figures are served at `/v1/execution-quality` through the [API](../architecture/api-and-sdk.md). Every aggregate mentioned above links down to the individual fills behind it in [the Ledger](../walkthroughs/use-the-ledger.md).
