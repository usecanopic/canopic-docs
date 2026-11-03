---
description: One reference list of the terminology used across these docs, organised by subject.
---

# Glossary

Entries are organised into subject areas, and each area lists its terms from A to Z.

## Market structure

| Term | Definition |
| --- | --- |
| **Anchor vault** | A per-asset contract, also called a market vault, that holds inventory in both the Stock Token and USDG and quotes buy and sell prices around the oracle mid. See [Market vaults](../trading/market-vaults.md). |
| **Clip** | The maximum size a single swap can draw from a vault. Larger orders are routed to RFQ for a quote. |
| **Maker** | An attested market maker that signs RFQ quotes off-chain and settles them atomically on-chain. |
| **RFQ** | Request for quote. A maker's quote is an EIP-712 signature that expires within seconds; the taker submits it and the trade settles atomically. See [Large orders and signed maker quotes](../trading/large-orders.md). |
| **Router** | `SwapRouter`, the sole entry point. It checks eligibility, compares the vault quote with the RFQ quote, and settles whichever is better. |
| **Vault share** | A token in the style of ERC-4626 that an LP receives on deposit, representing a pro rata claim on the vault's inventory and accumulated spread. |

## Pricing

| Term | Definition |
| --- | --- |
| **Band** | The permitted distance between a fill and the oracle mid. A fill that would fall outside it causes the transaction to revert. |
| **Execution quality** | The signed difference, in basis points, between a fill and the oracle mid at the moment its transaction was included. Canopic publishes this figure for every fill without exception. See [Execution quality reports](../transparency/execution-quality.md). |
| **Half-spread** | The gap between the mid and one side of a quote. All Canopic spread parameters are expressed this way, as half-spread in basis points. |
| **Inventory skew** | The amount by which a vault's holdings have drifted from their target mix. Quotes lean to correct it, offering better prices on trades that bring the vault back into balance. |
| **Mid** | An asset's guarded oracle price, as published by `OracleAdapter`. Without a mid, the vault does not quote. |
| **Quote asset** | USDG. Every pair is priced against it, so it is the unit for displayed prices and for fee collection. |
| **Tier** | A listing grade of A, B or C. An asset's grade sets its base spread, clip, band and caps. See [Listing policy](../assets/listing-policy.md). |

## Market hours and data

| Term | Definition |
| --- | --- |
| **Data Feeds** | Chainlink's push-model on-chain pricing service. Stock Token prices come from its equity feeds, which update 24/5. |
| **Data Streams** | A low-latency Chainlink service where reports are fetched on demand (v11 RWA schema). Each report carries a `marketStatus` field, which Canopic uses to classify the session. |
| **Regime** | A market's current pricing mode: `OPEN`, `EXTENDED`, `CLOSED` or `HALTED`. Each mode carries its own spread multiplier and clip size. See [Market hours and pricing modes](../trading/market-hours.md). |
| **Session** | The oracle's reading of whether the underlying's home market is in regular hours, extended hours or closed. Each regime is derived from the session. |

## Assets and eligibility

| Term | Definition |
| --- | --- |
| **Attestation** | Proof, recorded through the Ethereum Attestation Service, that a wallet passed off-chain checks for a given role: trader, LP or maker. It holds no personal information. See [Eligibility checks](../architecture/eligibility.md). |
| **ERC-8056 multiplier** | The value a Stock Token returns from `uiMultiplier()`, giving how many underlying shares one token represents. Splits and dividends appear as adjustments to it, and Chainlink prices already reflect it. |
| **Stock Token** | A tokenized listed share issued by Robinhood Assets (Jersey) Ltd. In legal terms it is an ERC-20 debt security, backed one to one by actual shares held at a US custodian. See [Stock Tokens](../assets/stock-tokens.md). |
| **USDG** | Paxos Global Dollar, a stablecoin regulated under MiCA that is issued natively on Robinhood Chain rather than bridged in. See [Settling in USDG](../assets/usdg.md). |
