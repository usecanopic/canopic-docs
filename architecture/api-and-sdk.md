---
description: How external software connects to the venue, covering the public quote API, the TypeScript SDK and the maker protocol.
---

# API and SDK for builders

Without an API, a venue is only a website. With one, Canopic becomes infrastructure. Any aggregator, wallet, agent or portfolio tool is quoted exactly what an app user is quoted, and its trades clear through the very same public router. Nothing is reserved for partners, and no endpoint carries special privileges. The reference app itself relies on precisely the surface documented here and on nothing more.

## Endpoint summary

| Endpoint | Response |
| --- | --- |
| `GET /v1/quote` | Full price with every component listed, and the transaction data to sign |
| `/v1/markets` | Listed markets with their tiers, parameters and regimes |
| `/v1/fills` | Paginated trade history, each fill with its breakdown |
| `/v1/execution-quality` | The data that feeds [Execution quality reports](../transparency/execution-quality.md) |
| `/v1/eligibility/:address` | Whether the address would pass its role checks, without sending anything |
| `wss://api.usecanopic.com/v1/stream` | Live push of fills, regime changes and per-market quote updates |

## Fetching a quote

```
GET /v1/quote?tokenIn=USDG&tokenOut=AAPL&amountIn=2000000000
```

```json
{
  "amountOut": "9873400000000000000",
  "breakdown": {
    "mid": "202.41",
    "spreadBps": 10,
    "skewBps": -2,
    "feeBps": 2,
    "regime": "OPEN",
    "oracleRound": "0x…"
  },
  "venue": "vault",
  "rfq": { "quote": { "…": "…" }, "signature": "0x…" },
  "validUntil": 1788316201,
  "tx": { "to": "0xTradeGateway…", "data": "0x…" }
}
```

The response includes:

* the price broken into its components,
* whichever venue offered the best result,
* when a maker won, its quote and the signature that will be verified on-chain,
* signable calldata with the trader's limit already filled in.

A quote is only ever a preview. At settlement the chain recomputes or re-verifies each part, so a faulty quote can at worst cause an integrator's transaction to revert. A bad fill is not possible.

## Live stream

Subscribing to `wss://api.usecanopic.com/v1/stream` delivers fills, regime changes and per-market quote updates at the cadence of the feeds. The app has no private channel of its own; its tickets listen to this same stream.

## TypeScript SDK

```ts
import { Canopic } from "@canopic/sdk";

const canopic = new Canopic({ chainId: 4663, signer });
const quote = await canopic.quote({ tokenIn: "USDG", tokenOut: "AAPL", amountIn: 2_000_000000n });
console.log(quote.breakdown);          // mid, spread, skew, fee and regime
const receipt = await canopic.swap(quote, { maxSlippageBps: 15 });
```

The SDK takes care of quoting, Permit2 signing, the eligibility preflight and decoding receipts, and it offers typed events to anyone consuming the indexer. Releases use semver and each comes with a public changelog, so nothing breaking lands without notice.

## Maker connections

Each maker holds open a WebSocket session (authenticated), hears every request as it is broadcast, and replies by signing under EIP-712:

```
→ { "type": "rfq", "id": "…", "tokenIn": "USDG", "tokenOut": "NVDA", "amountIn": "250000000000" }
← { "type": "quote", "id": "…", "quote": { …EIP-712 fields… }, "signature": "0x…" }
```

Access requires a `MAKER` attestation. [Quote as a maker](../walkthroughs/quote-as-a-maker.md) documents the message schema, the signing domain and how nonces work. Nothing is lost by losing an auction, launch carries no obligation to quote, and makers with a reliable record gain routing priority.

## Access keys and rate limits

Anyone may call the quote endpoints, subject to per-IP rate limits. With an authenticated key those limits rise and the WebSocket becomes available. The only reason keys exist is so we know who an integrator is when helping them or dealing with abuse. Pricing ignores them entirely, which anyone can verify, because vault pricing comes from a public contract read.

## Versioning

`/v1` will not change in breaking ways. We give no less than 90 days of changelog notice before retiring anything. Each SDK release is locked to one specific API version. Per-deployment contract addresses are listed under [Deployments and versions](deployments.md).
