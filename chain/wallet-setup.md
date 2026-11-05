---
description: Robinhood Chain reference: network ID, chain behaviour, supported standards and the Chainlink products Canopic uses.
---

# Wallet and RPC setup

{% hint style="info" %}
Treat [docs.robinhood.com/chain](https://docs.robinhood.com/chain/) as the source of truth for RPC endpoints, explorer links and Robinhood's own contract addresses. Canopic's addresses are recorded in `contracts/deployments/4663.json`, with an explanation on [Deployments and versions](../architecture/deployments.md). Verify every address against those references before sending funds.
{% endhint %}

## Network details

| Network | Chain ID | Status | Canopic deployment |
| --- | --- | --- | --- |
| Robinhood Chain | 4663 | Mainnet since 1 July 2026 | Production contracts, live |

## Chain characteristics

| Characteristic | Value |
| --- | --- |
| Execution | EVM-equivalent Arbitrum Nitro; the ArbOS 40 release brings EIP-7702 |
| Settlement layer | Ethereum |
| Data availability | Ethereum blobs (the chain runs as a rollup) |
| Block time | About 250 ms |
| Preconfirmation latency | About 100 ms |
| Gas token | ETH |
| Median fee per transaction | About $0.001 |
| Withdrawal window to L1 | 7 days |
| Sequencer | One sequencer, run by Robinhood, which applies sanctions screening |
| Censorship escape | L1 delayed inbox, forcing inclusion after 24 hours |
| Explorer | Blockscout |

## How Canopic handles time and calldata

* **`block.timestamp`** is the only clock: quote expiries, staleness limits, grace periods and the timelock all read it.
* **`block.number`** is never used for timing, because on Arbitrum it reports Ethereum's block rather than the L2's.
* **`ArbSys.arbBlockNumber()`** is called only in the uncommon cases that need the true L2 height, for example event metadata.
* **Calldata compression** is a priority since L1 data cost makes up most of each fee, which is why RFQ quote structs are packed and signatures are kept compact.

## Supported standards

| Standard | Role at Canopic |
| --- | --- |
| ERC-20 | Stock Tokens use 18 decimals; USDG uses 6 |
| ERC-8056 | `uiMultiplier()` carries Stock Token corporate actions |
| ERC-4626 | Accounting for vault shares, extended for two assets and in-kind redemptions |
| EIP-712 | Typed signatures on RFQ quotes |
| EIP-1271 | Lets a maker sign from a smart account and still have the quote verified |
| ERC-4337 | Account abstraction entry points, versions v0.6 to v0.8 |
| EIP-7702 | Lets a regular EOA delegate to and execute smart-account code |
| Permit2 | Settlement pulls funds from both maker and taker via allowance transfers |
| EAS | Stores the attestations used for eligibility |

## Chainlink services used

| Product | Purpose at Canopic |
| --- | --- |
| 24/5 equity Data Feeds | The mid used for vault quotes and band checks |
| v11 RWA Data Streams | Classifying sessions and cross-checking fills |
| Uptime feed for the sequencer | The post-outage grace period |
| CCIP | Planned bridge for gold and treasury tokens once Canopic lists them |
| ACE / CCID | A candidate adapter for eligibility |
