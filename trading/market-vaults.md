---
description: How each market's single-asset vault holds inventory and keeps two-sided prices available at all times.
---

# Market vaults

On Canopic, one market equals one vault. Every vault pairs a single Stock Token with USDG, and LPs supply both balances. Except when halted, a vault continuously offers a bid and an ask around the oracle mid, fills any trade up to its clip, and passes the spread it collects back to its LPs.

## Holding inventory instead of running a curve

Constant-product pools also keep inventory, but there the inventory determines the price. Each time the outside price shifts, such a pool is stuck showing a stale quote until arbitrageurs close the gap and pocket it. A market vault reverses that relationship: the oracle sets the price, and inventory only shapes the spread on either side of it. As a result the vault is never last to learn what an asset is worth, and LPs keep the spread rather than paying for another party's arbitrage.

This approach has an obvious cost, namely total reliance on the oracle price. All of the protections around that dependency are set out in [Oracle safeguards](../risk/oracle-safeguards.md).

## Staying within the inventory band

Each vault targets a configured mix of its two assets (half and half by value at the outset) and can never leave a hard band drawn around that target. Tier A launches with these values:

| Parameter | What it controls | Tier A launch value |
| --- | --- | --- |
| `targetRatioBps` | Share of the vault's value meant to sit in the Stock Token | 5,000 (50%) |
| `inventoryBandBps` | How far the mix may drift from target before one side stops quoting | 2,000 (±20%) |
| `maxClip` | Size ceiling for any one fill | 50,000 USDG |
| `dailyVolumeCap` | Volume ceiling per market while the guarded launch lasts | 2,000,000 USDG |

Consider a stretch of steady buying. Token holdings drop, USDG accumulates, and the vault ends up short the asset. [Skew in the price formula](price-formula.md) reacts by improving the bid and worsening the ask, which in effect pays makers and arbitrageurs to restore balance. Should one-way buying carry on until the vault hits the edge of its band, it stops quoting that side instead of taking on unlimited inventory risk. The opposite side remains available the whole time.

## Valuation and share accounting

Vault value is `usdgBalance + tokenBalance × mid`, using the guarded oracle mid. Share accounting in the style of ERC-4626 sits on top of that number:

* New shares are issued to depositors at today's value per share.
* Every bit of spread collected lifts what each share is worth.
* On redemption, shares are destroyed and the LP receives its pro rata cut of the USDG and of the tokens, in kind.

Because exits are paid in kind, an LP leaving never makes the vault trade, and no pause can trap LP capital. The full details and the economics are on [Providing liquidity to a vault](providing-liquidity.md).

## States a vault can be in

* **`ACTIVE`**: both sides quoted, subject to the limits of the current market hours mode.
* **`ONE_SIDED`**: at the edge of the band, so the vault quotes only the side that reduces the imbalance.
* **`HALTED`**: no quotes on either side. Four events lead here: oracle data going out of date, `oraclePaused()` being flagged by the feed, an open corporate action, or a pause by a guardian. Deposits and withdrawals keep working.
* **`RETIRED`**: the asset has been delisted. The vault will never quote again, and withdrawal is the only thing left to do.

`VaultFactory` creates each vault with the parameters set when the asset passed its [listing review](../assets/listing-policy.md). All of those parameters are `SettingsTimelock` values, and the timelock is the only way to change any of them.

## Properties enforced in code

The contract enforces each of the following, and the test suite proves it:

1. Whatever the state or parameters, a fill can never settle beyond the oracle band.
2. The spread earned is non-negative by design, so a trade can never reduce value per share.
3. Withdrawals remain available while the vault is `HALTED` or `RETIRED`.
4. Once the band limit is hit, the vault stops offering that inventory, no matter how imbalanced it has become.
