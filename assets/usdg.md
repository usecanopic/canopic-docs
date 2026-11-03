---
description: Why USDG is the single quote currency across Canopic, and how the decimal arithmetic works.
---

# Settling in USDG

Every Canopic market is quoted in **USDG**, the Paxos Global Dollar. It is the unit for spreads, fees and vault accounting, and a token-to-token trade always routes through it.

## Why USDG

**Native issuance.** USDG is minted directly on Robinhood Chain, not bridged from elsewhere, so settlement never depends on a bridge.

**The chain already uses it.** On Robinhood Chain, both Robinhood Earn and the Morpho lending markets run on USDG. A trader arrives holding the same dollar that Canopic prices in.

**Regulatory footing.** In the EU, USDG is covered by MiCA. Since the Stock Token jurisdiction list determines who Canopic can serve first, that standing is relevant.

**Straightforward mechanics.** As a plain ERC-20 without transfer fees or rebasing, it gives vault accounting exactly the foundation it requires.

## One quote asset, on purpose

A single quote asset concentrates liquidity rather than splitting each Stock Token across multiple stablecoin pairs. With one shared unit of account, quotes across markets are directly comparable, and verifying two-leg [routing](../trading/routing.md) on-chain remains feasible. The trade-off is reliance on a single issuer, listed alongside the venue's other dependencies in the [Risk framework](../risk/framework.md).

## A possible second quote asset

Each quote asset has its own market configuration. If another one were added, for instance USDC over CCTP after bridged liquidity is proven, or USDe, which is already deployed on the chain, it would form its own isolated group of markets without affecting the existing ones. A new quote asset must show proven bridged liquidity and clear the bytecode review applied to every listed asset. It then goes through the timelock as any parameter would.

## Decimal handling

| Parameter | Value |
| --- | --- |
| Decimals on USDG | 6 |
| Decimals on a Stock Token | 18 |
| Unit of a `OracleAdapter` price | Base units of the quote asset for one whole token |
| Value of a vault | `usdgBalance + tokenBalance × price / 10^18` |

Invariant tests exercise this math under both decimal configurations, including the rounding direction used when shares are minted or burned.
