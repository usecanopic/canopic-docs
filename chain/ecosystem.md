---
description: The Robinhood Chain protocols and services Canopic connects to, and the role each plays.
---

# Ecosystem integrations

Rather than rebuild what already exists on Robinhood Chain, Canopic connects to it. Some of these connections are part of how a trade settles. Others only matter to our supporting infrastructure off-chain.

## Summary

| Partner or service | Role |
| --- | --- |
| Chainlink | Reference prices, open or closed status for each market, sequencer health, and perhaps a policy engine for eligibility later |
| Paxos (USDG) | Quote asset |
| DEXs and aggregators (Uniswap, 1inch and others) | Arbitrage flow, plus reach through aggregated routing |
| Morpho Blue | The chain's lending and borrowing hub, whose users are natural swap candidates |
| Smart-account and gas sponsorship vendors | Gasless onboarding and batched calls |
| Robinhood Wallet | Main wallet for traders |
| Blockscout | Transactions visible to anyone |
| TRM Labs | Sanctions and anti-money-laundering screening of addresses |
| Indexer (Ponder or Envio HyperIndex) | Turning events into queryable data |
| Arbitrum grants and programmes | Builder programmes and audit subsidies |

## Prices and settlement

### Chainlink: prices and market status

Data Feeds and Data Streams supply 24/5 equity prices with the ERC-8056 multiplier built in, together with the `marketStatus` field that drives market hours. The Sequencer Uptime Feed determines how long markets pause once the sequencer recovers. The treasury and gold markets described in [Next asset classes](../assets/next-asset-classes.md) will come across via CCIP. The eligibility registry might later gain an adapter for Chainlink ACE and CCID, but only after those products reach general availability on this chain, which has not happened yet. See [Oracle safeguards](../risk/oracle-safeguards.md) for detail.

### Paxos: the USDG quote currency

USDG is chain-native, MiCA-regulated, and already funds the chain's lending markets, so it is the dollar traders tend to arrive with. Co-marketing with Paxos is part of the launch plan. See [Settling in USDG](../assets/usdg.md) for detail.

## Order flow and reach

### Uniswap, 1inch and aggregators

Uniswap v2, v3 and v4 are live on the chain, as is 1inch. We want the arbitrage traffic that drags generic pools into line with Canopic's anchored prices. Each such trade also rebalances a vault, and skew pricing compensates the arbitrageur for that. Aggregator integration via the public router interface is a distribution objective on its own terms, because routed orders reach Canopic whenever its anchored quote is the best on offer.

### Morpho Blue: lending next door

Roughly $80M to $90M in RWA deposits back Robinhood Earn, and those deposits are held in Morpho, home to all of the chain's USDG lending markets. Anyone moving money back and forth between earning yield and holding Stock Tokens needs somewhere to swap, and that is the job Canopic does.

### Robinhood Wallet: how traders arrive

Canopic's front-end connects straight to Robinhood Wallet, and a listing in the wallet's dApp directory is one of our distribution aims. We expect early demand to come from holders who already moved their Stock Tokens out of the Robinhood app into a wallet they control.

## Accounts and getting started

Canopic works with both EIP-7702 and the ERC-4337 entry points. On the front-end, Alchemy Account Kit provides embedded accounts and sponsored gas, and ZeroDev serves as the fallback. Placing a first trade needs only one sponsored user operation, which combines the swap with the Permit2 approval. RFQ quote signatures from smart accounts are verified via EIP-1271.

## Visibility and compliance screening

### Blockscout: public receipts

Open any fill, fee accrual or settings change in the Ledger and you can click through to the underlying Blockscout transaction.

### TRM Labs: sanctions and AML

TRM runs screening at the sequencer layer of the chain. Separately, the KYC provider behind Canopic's eligibility attestations screens users when it issues an attestation and again whenever one is renewed, giving two independent layers.

## Indexing on-chain data

We are evaluating two indexers to convert on-chain events into the data our services consume: a self-hosted TypeScript option (Ponder), or the hosted Envio HyperIndex service for Robinhood Chain. Whichever we pick, its output powers three things: our keepers, the Ledger, and the dashboards that report execution quality. Details are in [Off-chain services](../architecture/offchain-services.md).

## Arbitrum programmes

Because Robinhood Chain is an Arbitrum Orbit chain, Canopic can take part in Arbitrum programmes. We plan three moves there: applying to the Arbitrum Foundation for an audit subsidy, entering the Arbitrum Open House 2026 buildathon track, and joining Chainlink BUILD.
