---
description: The stateless helper processes that operate next to the contracts, and why none of them asks for your trust.
---

# Off-chain services

Three services operate around the contracts under a shared constraint: they exist to make the venue faster and smoother, never to raise the amount of trust it demands. If one fails, things become less convenient, but every guarantee remains intact.

## Quote service: previewing prices

### Its job

This service answers one question: what would a given swap cost right now? Answering it involves four steps. A simulated `SwapRouter` call is run over current oracle and vault data. Connected makers are sent an RFQ. Their signed replies are accepted for a few hundred milliseconds. Finally, the best overall quote comes back with every component itemised. Every price shown on the Trade ticket or returned by the public [API](api-and-sdk.md) originates here.

### Why trusting it is unnecessary

1. It contains nothing worth stealing, since it holds neither signing keys nor balances. What it outputs is a preview and, in the RFQ case, maker signatures that the chain verifies independently.
2. At fill time the router recalculates vault pricing on-chain. An incorrect preview therefore runs into the trader's signed limit and reverts instead of turning into a poor fill.
3. Nobody can hide the vault route. Whenever the vault beats every submitted quote, the router fills against the vault.

### When it is unavailable

Any interface able to reach the contract can still trade against the vaults, and prices do not change. The only losses are RFQ aggregation and the convenience features.

## Keeper: timely triggers

Permissionless calls are what fire staleness halts, regime changes and the close of the sequencer grace period. Sending them as soon as they become due is all the keeper does. Regime is also re-evaluated within the transaction of every quote. A keeper that lags therefore cannot cause a trade to be priced under the wrong session; at most, the Ledger shows the new state a little late. Because the reference code is published, anyone may run an additional keeper, or ten, without asking.

## Indexer: from events to history

One of two indexers, either Ponder (TypeScript, self-hosted) or Envio HyperIndex, converts fill and parameter events from Robinhood Chain into the [Ledger](../walkthroughs/use-the-ledger.md), the [execution quality](../transparency/execution-quality.md) dataset, and LP statistics for each market. Each displayed figure links to the event it came from, so a sceptic can reconstruct everything from Blockscout without relying on the indexer.

## Connecting makers

Makers connect to the quote service through the WebSocket protocol described in [API and SDK for builders](api-and-sdk.md). The service pushes each request to them, and they answer with a signature over their price. Every maker operates its own gateway; Canopic only forwards requests and ranks the answers. If a maker drops its connection, it just stops taking part in the auction and nothing else is affected.

## Monitoring

Operational monitoring relies solely on public state, meaning the same data anyone can query:

* divergence and staleness of oracles,
* each vault's skew relative to its bands,
* whether any halt is active,
* uptime of the sequencer,
* the distance between fills and the band,
* shifts in execution quality over time.

Alerts go to whichever guardian signer is on call. Nothing we watch is hidden from you: every signal that matters is on-chain already, so no privileged telemetry is needed. That is a design choice as much as an operational one.

## Self-hosting the stack

All of these services are open source. Clone the public repositories and you can host your own quote service, keeper and indexer, connected to an RPC provider of your choosing, with no reliance on us. For a worked example of a client built on these APIs, look at [the Canopic app](app.md).
