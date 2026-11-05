---
description: The legal entities behind Canopic, the role of each, and the planned path for control.
---

# Legal structure

Two distinct organisations back the protocol, and each has its own responsibilities.

## Canopic Foundation

Stewardship of the immutable contracts and of protocol governance sits with the foundation. It also holds the timelocked parameter multisig and the guardian, until both pass to the governance module.

## Canopic Labs

As the operating company, Canopic Labs develops and operates the platform, the quote service, the indexer and the keeper software. It employs the team and holds any licence that is required.

## Why two entities

| Goal | How the split achieves it |
| --- | --- |
| Protecting governance | The contracts embody non-custody and immutability. A foundation is their home so that the business pressures on the builder company never reach protocol governance. |
| Limiting liability | The operating company is what users interact with, through a front-end, and it is the party that obtains licences wherever they are needed. Keeping it separate reduces the protocol's exposure. |
| Continuity without the company | If the operating company ceased to exist, the venue would still function. Quotes from the vaults depend only on on-chain state, so they would continue, and nothing could put conditions on withdrawals. The quote service, keeper, indexer and front-end are all open source, which means any third party could take them over. Listings would slow and convenience would suffer, yet the market would carry on. |

## The path for control

**Today.** The foundation's multisig, behind a timelock, is the only route for parameter changes. Every proposal goes out publicly with a written rationale attached.

**Next.** Control of parameters transfers to a governance module. These guarantees survive the handover unchanged:

* the timelock;
* a public rationale for every change;
* zero access to user funds;
* no power to put conditions on withdrawals;
* no power to touch the outcome of an individual fill.
