---
description: How the on-chain contracts and the helper services fit together, and exactly where trust stops and verification begins.
---

# Architecture overview

At its core, Canopic is a small set of contracts deployed on Robinhood Chain whose code can never be upgraded. A few stateless helper processes surround them, and their only job is convenience. One principle shapes the whole design: **enforcement belongs to the contracts, while the services just assist.** You could shut down every service and no guarantee would weaken. You would lose some comfort, but never your funds and never fair execution.

## The system in one diagram

```
          Traders, LPs, Makers, Integrators
                        │
        ┌───────────────┼────────────────────┐
        │               │                    │
   Platform UI     Quote service        Maker gateways
   (Next.js)       (stateless)          (RFQ streaming)
        │               │                    │
        └───────────────┼────────────────────┘
                        │  signed transactions only
   ┌────────────────────▼──────────────────────────┐
   │              Robinhood Chain                  │
   │                                               │
   │  SwapRouter ── MarketVaults ── RfqSettler    │
   │       │               │               │       │
   │  EligibilityRegistry       │          ProtocolTreasury  │
   │       │            OracleAdapter                   │
   │  SettingsTimelock (timelock)                    │
   └───────────────────────────────────────────────┘
                        │
        Indexer (Ponder / Envio) ── Trade explorer,
        execution-quality dashboards, keeper service
```

The layer drawn above the chain reaches it only by submitting signed transactions. The layer drawn beneath it does nothing except read data the chain has already committed.

## Trust, party by party

| Party | The job you count on them for | Things they cannot do |
| --- | --- | --- |
| Chainlink | Delivering market status and price data, which pass through the guards before use | Touch funds; the band and the halt rules absorb any faulty print |
| Robinhood (sequencer) | Ordering transactions and keeping the network up | Take funds; force-inclusion means they can only delay transactions, not block them for good |
| Robinhood Assets, as token issuer | Defining and backing Stock Tokens | Reach into the venue's contracts; see [Issuer exposure](../risk/issuer-exposure.md) |
| Attestation issuer | Making correct eligibility calls | Price, settle or custody anything whatsoever |
| Canopic Labs, as service operator | Operating the convenience layer | Change what a fill pays, hold user assets, or block someone from leaving |
| Timelock multisig | Adjusting parameters openly after the waiting period | Transfer funds, freeze withdrawals or bypass the waiting period |

## Invariants across every contract

[On-chain components](onchain-components.md) walks through each contract and its one task. Across the full set, four properties always hold:

1. **Code is frozen; parameters wait.** Nobody can upgrade the vault or settlement logic. Behaviour shifts only through values stored in `SettingsTimelock`, which are guarded by the timelock and each come with a published explanation.
2. **A single entry point for prices.** Every price a contract consumes passes through `OracleAdapter` and its guards. There is no other route.
3. **A single entry point for permissions.** `EligibilityRegistry` alone decides whether an address is allowed to act. No other contract forms its own view on that question.
4. **Pausing only stops things.** The guardian has the power to halt quoting and settlement, nothing more. Withdrawals remain available the whole time, and no role anywhere can move user funds.

## Helper services

The contracts are accompanied by three services, and not one of them holds anything that could reach funds.

| Service | Function | Why it poses no risk |
| --- | --- | --- |
| Quote service | Previews what the router would pay and assembles maker quotes, feeding both the app and the API | Every input is recomputed or rechecked by the router, so the service has no influence over a fill. See [Off-chain services](offchain-services.md). |
| Keeper service | Submits the calls that move markets between regimes and trip halts | The functions it calls are permissionless, its reference code is public, and anybody may operate one. |
| Indexer | Reads chain events to produce the Ledger and the execution-quality datasets | If it fails it may show incorrect figures, yet it is incapable of settling anything incorrectly. |

## Failure handling

Each piece of the system has a fallback worked out in advance. Oracle problems trigger halts, sequencer trouble has a grace path, direct contract calls stand in for the services, and the band constrains parameters no matter what else happens. [Risk framework](../risk/framework.md) holds the complete catalogue.
