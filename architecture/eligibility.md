---
description: How wallets qualify as traders, LPs or market makers, and how qualification works without putting personal data on-chain.
---

# Eligibility checks

## The reason for venue-level checks

The Stock Token contracts themselves place no limit on who can receive a transfer. Robinhood polices who may hold them in two places: its own app, and the issuance and redemption process (KYC'd in both cases). Any other venue that lists the tokens must therefore do its own enforcing. Canopic does this at the **protocol boundary**: every swap, deposit, RFQ settlement and vault share transfer passes through a check.

## Roles

* **`TRADER`** (live): individuals and companies who have completed KYC and are not based in a restricted jurisdiction. This tracks the eligibility list that governs who can hold Stock Tokens.
* **`LP`** (live): liquidity providers. Vaults carry inventory exposure, so at launch only professional clients receive this role.
* **`MAKER`** (live): professional market makers accepted into RFQ.
* **`RELAYER`** (live): intended for software that transacts for someone else, like an integrator or an account-abstraction bundler. The contracts define it and it can be attested, yet at present no contract function checks for it.

## Which check runs where

| Action | Required roles |
| --- | --- |
| `swap` | `TRADER` for the trader, and for the recipient too if that is a different address |
| RFQ `settle` | Both sides: `TRADER` for the taker of the quote, `MAKER` for its signer |
| Vault `deposit` | `LP` for the address providing the assets |
| Vault `withdraw` | None. Owning the shares suffices, since leaving is a right you hold, not a privilege someone extends |
| Vault share transfer | `LP` for the recipient |

## The shared eligibility call

```solidity
function isEligible(address account, bytes32 role) external view returns (bool);
```

`SwapRouter`, `OracleVault` and `RfqSettler` all invoke it for the relevant parties, and any failure reverts. The app calls the same view before requesting a signature, so an ineligible user learns the reason in advance instead of spending gas to find out. No view requires permission to call, so any explorer, indexer or aggregator can query what it needs.

## Contents of an attestation

The credential behind eligibility is an attestation on the **Ethereum Attestation Service**, in a form that also works with ONCHAINID. After finishing identity, sanctions and residency screening, the KYC provider issues one containing:

| Field | Detail |
| --- | --- |
| Wallet address | The account being attested |
| Role | Any of the four roles listed earlier |
| Jurisdiction class | The actual country is included only when the user chooses to share it |
| Investor class | Included when relevant |
| Expiry | Each attestation has an end date; ongoing re-screening issues a renewal |

Personal data stays off it entirely: no names, no documents, no identifiers. A revocation applies instantly.

### Private proofs of eligibility

Wallets that want more privacy can use Privado ID or zkPass credentials instead. These prove a statement of the form "eligible in jurisdiction class X, investor class Y" and disclose nothing else, neither the raw attributes nor the identity of the screening provider. Both this route and the standard attestation are valid in the registry.

## Interchangeable policy adapters

The registry reaches policy through an adapter interface, so the engine behind it can be replaced without altering the core:

1. **`AttestationBook`** (currently in production). Some networks, Robinhood Chain testnet among them, have no EAS deployment yet, so this adapter keeps its own attestation records and reproduces EAS behaviour. Issuers whitelisted in `SettingsTimelock` create and revoke entries holding role, jurisdiction class, investor class and expiry. An entry is valid only if it has not expired, has not been revoked and comes from an approved issuer.
2. **ONCHAINID adapter**: handles ERC-3643 identities.
3. An adapter for **Chainlink ACE / CCID**, another policy engine that governance may switch to via the timelock if required.

Approved issuers and schemas are held as a value in `SettingsTimelock`. Making policy stricter or changing providers is thus a recorded change that sits out the timelock, with no redeployment needed.

## Tokens that carry transfer rules

Suppose Stock Tokens or bridged RWAs begin using ERC-7943 (uRWA) or ERC-3643 hooks. The vaults and `RfqSettler` are ready for that: they already query `canTransfer` and `canReceive` before moving tokens, and the issuer would then place them on its allowlist. The token's rules and the venue's rules would then both be enforced, with no trust required in either direction. See [Next asset classes](../assets/next-asset-classes.md) for more.

## Excluding restricted jurisdictions

This relies on several independent layers:

* Wallets located in a restricted jurisdiction never receive an attestation.
* Every wallet self-attests its location as part of onboarding.
* The app geo-fences restricted jurisdictions by IP address.
* Sanctions screening also happens independently at the sequencer.

Responsibility for the restricted list sits with the attestation issuer, who keeps it aligned with where the Stock Token issuer says its tokens are available. [Regulatory stance](../compliance/regulatory-stance.md) covers the broader context.
