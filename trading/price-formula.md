---
description: The single formula that produces every vault price, plus where on-chain each of its inputs is read from.
---

# The price formula

All vault prices are produced by the same formula. No step in it relies on discretion: every input is publicly readable from a contract, so any quote can be reproduced by anyone working from chain state.

## Ask and bid

```
ask = mid × (1 + (baseHalfSpread × regimeMultiplier + skewTerm + feeBps) / 10_000)
bid = mid × (1 − (baseHalfSpread × regimeMultiplier − skewTerm + feeBps) / 10_000)
```

## Inputs and their sources

| Input | Read from | Purpose |
| --- | --- | --- |
| **`mid`** | `OracleAdapter` (Chainlink data after guard checks) | The asset's current fair value |
| **`baseHalfSpread`** | A per-tier parameter | Covers the cost of holding inventory during the regular session, plus any residual oracle latency |
| **`regimeMultiplier`** | The [market hours](market-hours.md) state | Compensation for jump risk in extended hours and when the home exchange is shut |
| **`skewTerm`** | Vault inventory | Charges more for moving the vault further from target; its sign makes it negative on the side that corrects the imbalance |
| **`feeBps`** | `SettingsTimelock` | The protocol fee, shown as a separate line on the ticket |

## Starting parameters

Every value below is expressed in basis points, varies with the asset's [tier](../assets/listing-policy.md), and can be changed only via the timelock.

**Per tier**

| Tier | Typical members | Regular-hours clip | Half-spread ahead of the multiplier |
| --- | --- | --- | --- |
| A | SPY, QQQ, AAPL, MSFT, NVDA and similar | 50,000 USDG | 10 bps |
| B | Highly liquid single stocks outside Tier A | 20,000 USDG | 20 bps |
| C | All other, less liquid Stock Tokens | 5,000 USDG | 40 bps |

**Per market hours mode**

| Mode | Multiplier on spread | Multiplier on clip |
| --- | --- | --- |
| `OPEN` | 1.0 | 1.0 |
| `EXTENDED` | 1.5 | 0.75 |
| `CLOSED` | 3.0 | 0.5 |
| `HALTED` | No quote | 0 |

For a rough feel: trading a Tier A asset during the regular session costs about 10 bps from mid, plus the fee. Run that same trade on a Saturday and the figure climbs to around 30 bps, and the ticket explains the difference. Treat these as opening values. Measured outcomes are published in [Execution quality reports](../transparency/execution-quality.md), and any change they prompt has to go through the timelock.

## Skew as the rebalancing tool

`skewTerm = maxSkewBps × (currentRatio − targetRatio) / inventoryBand`

As the vault moves away from target, skew grows linearly until it hits its cap of `maxSkewBps` (15 bps for Tier A at launch) at the band edge. What matters most is the sign. Suppose the vault is heavy in tokens. Then buying from it gets cheaper, and selling into it pays less. Flow that restores the target mix is rewarded with a better price; flow that worsens the mix is charged extra. Once the band edge is reached, quoting on the harmful side switches off entirely.

Nothing else is involved in rebalancing. There is no auction, no keeper trading and no manual intervention. Skew just pays outside traders to do the job.

## The fixed oracle band

Apart from the spread formula, `SwapRouter` holds every price to a hard band around the mid. Tier A starts with 75 bps; Tiers B and C get wider bands. Neither a vault fill nor an RFQ fill can settle beyond it.

Think of the band as the final safeguard against errors such as a broken quote, a forged maker signature or a mistyped parameter. It does not matter which upstream component failed: if the fill price is further from the guarded oracle price than the band allows, the transaction reverts.

## What the ticket shows

On the ticket, the formula is broken out term by term:

* the mid and the oracle round that produced it;
* how wide the spread is, as bps plus its USDG equivalent;
* what the protocol fee comes to, again as bps plus its USDG equivalent;
* a badge showing the market hours mode;
* the all-in price, which the slippage limit is measured against.

What settles on-chain is exactly what the user signed. Should state change so that the fill would be worse than the signed limit, the transaction reverts instead of going through.
