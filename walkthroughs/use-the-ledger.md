---
description: The Ledger exposes all Canopic activity publicly, and you can browse it without signing in.
---

# Use the ledger

The Ledger keeps a record of every fill, the current state of each market, every parameter and each change made to them. All of it is built from chain events, with links back to the originals. Browsing needs no wallet, since nothing here belongs to anyone privately. It is where the phrase "transparently on-chain" either holds up under inspection or falls apart.

In the app, open **Ledger** from the sidebar. The page groups its content into a **Trade record** of itemised trades (filterable by side and by liquidity source, vault or maker), a **Parameters in force** panel, and a **Change history** of governance actions.

## Per market

| Item | What it shows |
| --- | --- |
| Live quote | What a trade would cost at this second: bid, ask and mid, itemised using the router's own pricing |
| Regime | The session state in force now, how long it has lasted, and why it was chosen (the stream's market status plus the feed timestamp) |
| Inventory | The vault's mix relative to its target and band, along with which way skew points and how large it is |
| Parameters | Live values pulled from `SettingsTimelock`: tier, spreads, clip, caps, band |
| History | Itemised fills, spread revenue broken out by regime, and a halt log stating why each halt occurred |
| Listing record | Why the listing got its tier, what the bytecode review found, the issuer's disclosure, and reserve proofs for tokens that have a feed |

## Venue-wide

* **Activity.** Volume, number of fills and revenue, sliced by market, by liquidity source (vault or RFQ) and by regime.
* **Issuer exposure.** A single figure for total vault inventory exposed to Robinhood Assets (Jersey) Ltd, as set out in [Issuer exposure](../risk/issuer-exposure.md).
* **Execution quality.** The dataset behind the [execution quality reports](../transparency/execution-quality.md): how far each fill landed from the oracle mid, both aggregated and as a downloadable file.
* **Governance log.** Proposals to change parameters, both live and already carried out, each showing the stated reason, the waiting window and the execution record. In the app this appears as **Change history**.

## Following a number to its source

Every figure is clickable and leads to the Blockscout event, transaction or contract read it came from. The Ledger is powered by the open-source indexer, so anybody can run it against any RPC and confirm the results independently. If the Ledger and the chain ever disagree, the chain is authoritative, and the discrepancy should be filed as a bug.

## Getting the raw data

For unprocessed access, query the [API](../architecture/api-and-sdk.md) endpoints `/v1/markets`, `/v1/fills` and `/v1/execution-quality`, or grab the CSV exports. Signed-in users can additionally export the full LP history of each of their positions. We welcome academic and independent study of this data. In our view, an RWA market whose past cannot be reconstructed by outsiders does not really have a past.
