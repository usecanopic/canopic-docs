---
description: The networks the contracts run on, how to verify any deployment, and the only official sources for addresses.
---

# Deployments and versions

{% hint style="warning" %}
Only three sources publish Canopic contract addresses. They are the file `contracts/deployments/4663.json` (inside the open-source repository), [usecanopic.com](https://usecanopic.com), and the registry this page links to. Treat an address found anywhere else as hostile, and verify before you approve any transaction.
{% endhint %}

## Supported networks

| Network | Chain ID | Role |
| --- | --- | --- |
| Mainnet (Robinhood Chain) | 4663 | Production |
| Testnet (Robinhood Chain) | Listed in the repository | Permanent staging environment; every release lands here before mainnet |

Parameters on testnet are identical to mainnet's. The deployment there trades Robinhood's testnet Stock Tokens along with assets from the faucet. That gives integrators a free rehearsal space for every step: attestation, quote, swap and withdrawal.

## Verifying a deployment

For every contract, `contracts/deployments/4663.json` records:

* the deployed address,
* the source commit,
* the compiler configuration,
* arguments passed at construction,
* the deployment transaction.

Each contract is verified on Blockscout, and with every release a CI job checks that on-chain bytecode matches the tagged source. You can confirm for yourself that the audited code is the deployed code instead of taking our word for it.

## Release process

| Stage | Action |
| --- | --- |
| 1. Tag | A version tag is cut, its diff goes through audit or review, and the code is made public |
| 2. Testnet | Testnet receives the build first; invariants and a scripted full-journey test are run against real testnet oracle data |
| 3. Mainnet | Deploy the contracts using `CREATE2` so addresses match across environments |
| 4. Configure | Constructors set no parameters; every value arrives through the timelock, producing a complete governance history from block one onward |
| 5. Publish | Update the deployment record and refresh verification, then announce the release, never before |

## Changes after launch

Because logic cannot change, every post-launch release takes one of two forms. Either the factory creates a new market, which touches parameters only and adds no code, or a new protocol version is deployed next to the current one and LPs migrate on their own schedule (see [On-chain components](onchain-components.md)). Previous versions cannot be disabled remotely, as no mechanism for doing so exists.

## Client configuration source

Clients look up addresses, listed markets, tiers and oracle adapters on-chain, from `SettingsTimelock` and the factory. The SDK ships without a hardcoded market list and reads the chain instead. So even if someone hands a client stale or doctored settings, misrouting a user to a bogus market would trip the signature and address checks.
