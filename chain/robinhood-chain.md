---
description: What Robinhood Chain brings to a venue anchored on oracle prices, and the missing pieces Canopic supplies itself.
---

# Building on Robinhood Chain

Put briefly, this chain lets people keep real-world assets in their own wallets, as plain ERC-20 tokens that a regulated company issues and Chainlink prices. An oracle-anchored venue needs precisely that mix. No other chain offers it today, and none offers it alongside the distribution of the broker that issues the assets.

## The alternatives we considered

* **Solana.** The xStocks sold via Kraken are issued by an outside party, not by a broker with its own customers, and most of their volume runs through centralised order books or AMMs built for any token.
* **Base.** Coinbase brought tokenized equities to Base on 24 August 2026. That is so recent that no venue layer sits on top of them yet.
* **Ethereum mainnet.** Liquidity in DeFi runs deepest on L1. Still, no broker issues equities natively there, and fees make a small RWA trade pointless.

Only on Robinhood Chain do the assets, pricing, quote asset, account abstraction and cost structure all fit together, and the issuing broker's distribution comes on top of that.

## Feature by feature

The numbers below are taken from Robinhood's docs and from the Arbitrum Foundation.

| Feature | Robinhood Chain today | Relevance to Canopic |
| --- | --- | --- |
| Architecture | Dedicated Arbitrum Nitro (Orbit) rollup settling to Ethereum, data posted as blobs; 7-day canonical withdrawal | Canopic inherits Ethereum's security and can use the usual Arbitrum tools |
| Maturity | Live on mainnet from 1 July 2026; TVL near $540M by mid-August 2026 (estimates range from $536M to $1.4B) | Canopic launches into existing liquidity and an active user base |
| Deployment | Permissionless and EVM-equivalent; works with Solidity, Vyper, Foundry and Hardhat, with Rust and WASM via Stylus | No gatekeeper between Canopic's contracts and the chain |
| Latency and fees | Around 250 ms blocks, around 100 ms preconfirmations, median fee about $0.001, ETH for gas | Quotes stay valid for several seconds, and sub-cent settlement makes small trades viable |
| Ordering | Robinhood runs the only sequencer, which orders FCFS and screens for sanctions; after 24 hours a transaction can be forced in via the L1 delayed inbox | Canopic plans for outages of up to 24 hours and can use the Chainlink Sequencer Uptime Feed |
| On-chain RWAs | **Stock Tokens**: 18-decimal ERC-20s issued by Robinhood Assets (Jersey) Ltd, backed 1:1 by US-custodied shares, offered in more than 120 countries | The assets Canopic lists, issued by a publicly listed broker |
| Corporate actions | Through ERC-8056 `uiMultiplier()`, a split shows up as a change in shares per token and dividends are reinvested automatically | No dividend bookkeeping and no ex-dividend drop to mishandle |
| Price data | Chainlink offers CCIP, the Sequencer Uptime Feed, 24/5 Data Feeds and v11 RWA Data Streams exposing `marketStatus`; each Chainlink price bakes in the multiplier | Session-aware anchor pricing at production grade from launch |
| Stablecoins | Paxos's USDG, chain-native and regulated under MiCA, already Morpho's lending dollar; USDe live; USDC via CCTP still *unconfirmed* | The unit every quote is priced in |
| Account features | EIP-7702 is enabled in ArbOS 40, ERC-4337 EntryPoints v0.6 to v0.8 are deployed, and both ZeroDev and Alchemy Gas Manager serve the chain | New users trade without holding gas, and a single user operation covers approval plus swap |
| Live protocols | Uniswap (v2, v3 and v4) is deployed, as are Morpho Blue, Lighter, Arcus and 1inch | Aggregators and arbitrage desks are already active when Canopic goes live |
| Compliance on-chain | Stock Tokens carry **no** on-chain restrictions (neither a transfer hook nor an allowlist); the only gates are Robinhood's app and its KYC'd primary channel for issuance and redemption | The protocol boundary is where Canopic must apply eligibility, and nobody else will do it |
| Chain economics | 10% of the chain's net revenue goes from Robinhood to the Arbitrum DAO; $1M committed to sponsor Arbitrum Open House 2026 | Builders can realistically obtain grants and audit subsidies |

## Gaps Canopic fills

An even-handed assessment also names what the chain leaves out:

* Nothing on-chain enforces Stock Token eligibility. Canopic adds that with its [eligibility checks](../architecture/eligibility.md).
* There is no built-in protection for sequencer outages. Canopic provides the post-recovery grace window and the delayed inbox path, described in [Chain outages and sequencer faults](../risk/chain-incidents.md).
* Nobody prices the gap while markets are closed. Canopic does, as explained in [Market hours and pricing modes](../trading/market-hours.md).
* Existing DEXs on the chain handle RWAs as if they were any other token. Correcting that is why Canopic exists, as set out in [Why this venue, and why now](../basics/why-now.md).
