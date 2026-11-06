---
description: Where Canopic's revenue comes from, what it pays for, and how income grows with activity.
---

# Business model

Canopic is paid in exactly two ways, each shown line by line and each a modest share of the value created: a charge on every fill, and a cut of what vaults earn in spread. Four practices are off limits for good. We never emit tokens, accept payment for order flow, act as counterparty to our users, or make money from their data.

## Revenue sources

Three sources exist, each launching at the rate shown:

1. **Swaps against a vault:** a 2 bps charge on notional. Revenue tracks overall volume.
2. **RFQ fills:** the same 2 bps on notional, so revenue tracks block volume.
3. **Cut of vault spread:** the protocol takes 10% of realised spread, so this line depends on volume and spread together. As a result, it is largest exactly when LPs are earning the most.

Each rate is a `SettingsTimelock` value guarded by the timelock. Every ticket displays them and every fill event emits them. For details, read [Fees and spreads](../trading/fees.md).

## Illustrative figures

| Case | Mean daily volume | Mean all-in half-spread | Annual fee revenue | Annual spread share | Annual total |
| --- | --- | --- | --- | --- | --- |
| Base | 10M USDG | 12 bps | ~730K USDG | ~438K USDG | ~1.2M USDG |
| Volume upside | 50M USDG | 12 bps | Not itemised | Not itemised | ~5.8M |

For scale: across all venues, on-chain trading in tokenized equities came to about $9B in the first eight months of 2026. Canopic's model needs just a small share of a market expanding by several hundred percent per year.

## What draws order flow

* **Structurally better RWA prices.** Because anchored quotes do not drift or hand LP value to arbitrageurs, Canopic can quote tighter than generic pools at an equal LP return. Aggregators route on price alone, so the tightest quote wins the trade.
* **Trading at all hours, priced for the session.** Tokens on the chain never stop trading, which makes overnight and weekend demand real. Canopic meets that demand at a disclosed premium instead of turning it away or mispricing it, something no other venue offers.
* **Distribution through channels that already exist.** Users on the chain hold Stock Tokens in self-custody after withdrawing them directly from a broker app, and Canopic reaches them through the wallet directory, aggregators and the API. Read [Ecosystem integrations](../chain/ecosystem.md).
* **Compounding integrations.** Once a wallet or agent is built on the [API](../architecture/api-and-sdk.md), it continues sending flow with no additional acquisition cost.

## Costs

Outgoings include audits and the bug bounty, oracle and data subscriptions, RPC and indexing, the KYC provider's fee for each attestation, and team salaries. Fees accrue in the `ProtocolTreasury` and pay for these items before anything else. After the module assumes control, fund allocation is recorded in the [settings change log](../transparency/settings-changes.md).
