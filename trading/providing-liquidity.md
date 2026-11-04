---
description: How to deposit into a market vault and withdraw again, where the returns come from, and precisely what risks you carry.
---

# Providing liquidity to a vault

Vaults fill trades using LP capital, and the spread from every fill goes back to the LPs who provided it. The goal is to pay LPs for a genuine service, supplying immediacy in a market that already has an external reference price, rather than for acting as the reluctant counterparty to other people's arbitrage.

## Depositing

Deposits are accepted per market from any address holding an `LP` attestation. Bring USDG, the market's Stock Token, or a combination. Whatever you bring is priced at the guarded oracle mid, and you receive shares based on today's value per share.

| Term | Detail |
| --- | --- |
| Deposit fee | None |
| Lock-up period | None |
| Share token standard | ERC-4626 |
| Permitted share recipients | Attested LPs only |

The formula for value per share is `(usdgBalance + tokenBalance × mid) / totalShares`, so spread income pushes it higher over time.

## Source of returns

With every fill, the realised spread minus the protocol's 10% [share of spread](fees.md) is added to value per share as it happens. There are no emissions and no points. What LPs earn is just what the market pays for immediacy.

The [Ledger](../walkthroughs/use-the-ledger.md) shows realised spread income per market in real time, so a vault can be judged on what it has actually earned rather than on projections.

## Withdrawing

When you burn shares, you get back an **in-kind, pro rata** slice of the vault's current holdings. Redeeming 1% of shares returns 1% of the USDG and 1% of the tokens. That design is intentional, and it brings three consequences:

1. The vault does not sell anything when you exit, so leaving moves no price and gives nobody anything to sandwich.
2. Exits are possible from any vault state, `HALTED` and `RETIRED` among them. LP capital cannot be frozen by a pause, a halt or any configuration choice.
3. Any skew in the vault is reflected in the asset mix you receive. An LP who prefers to end up purely in USDG can trade the token portion just as any trader would.

## The risks you carry

These are stated bluntly, because nobody should deposit without considering them:

| Risk | In practice | Mitigation |
| --- | --- | --- |
| Price exposure | Part of the vault is the Stock Token, so its value tracks the share price; structurally, LPs are long for roughly half of the time | Each market stands alone, and LPs choose which assets to back |
| Gap risk | Trades filled overnight or at weekends may be priced at levels the next open jumps away from | Closed-mode multipliers and reduced clips are there specifically to charge for this |
| Oracle risk | A wrong mid produces wrong prices | The measures described in [Oracle safeguards](../risk/oracle-safeguards.md), plus the band limiting what any one fill can cost |
| Issuer risk | Each Stock Token is a claim on the entity that issued it | Explained in [Issuer exposure](../risk/issuer-exposure.md); disclosed openly, not diversified away |
| Adverse flow | Sustained one-way flow holds the vault at its band edge | The vault quotes only one side rather than absorbing inventory without limit |

LPs are **not** exposed to leverage, liquidation, spillover from losses in other markets, or assets they did not select. Vaults never interact with each other.

## Limits while the launch is guarded

For the duration of the guarded phase, every market carries a TVL cap. Together with the daily volume caps, it limits total exposure while parameters are tuned against live data. Lifting these caps requires a timelocked decision, made as the [execution quality](../transparency/execution-quality.md) track record builds up and following the plan set out in [Milestones](../roadmap/milestones.md).
