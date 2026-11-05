---
description: The reasoning behind Canopic's approach to compliance, and the tools that enforce it.
---

# Regulatory stance

{% hint style="info" %}
This page explains how the protocol enforces its compliance rules. It is descriptive only and is not legal advice.
{% endhint %}

## Rules applied at the protocol boundary

Canopic never takes custody of anyone's assets. Nothing inside the tokens restricts who can hold them on-chain, which is why Canopic places its rules at the **protocol boundary**.

Stock Tokens are standard ERC-20s. On Robinhood's side, eligibility is screened in the Robinhood app, and minting or redeeming requires KYC. On-chain, however, any wallet can hold them, so each venue listing them has to decide for itself who is allowed to trade. For Canopic, the [eligibility registry](../architecture/eligibility.md) is where that answer lives, and every entry point consults it. It mirrors the issuer's jurisdiction list instead of drawing up a looser one.

## Requirements by role

* **Trading:** you need a `TRADER` attestation. It shows three things: KYC is done, you are not located in a restricted jurisdiction, and you qualify under the Stock Token eligibility list.
* **Providing liquidity:** an `LP` attestation, offered only to professional clients at launch.
* **Making markets:** limited to professional trading firms holding a `MAKER` attestation.
* **Receiving transferred vault shares:** the destination wallet needs an `LP` attestation.
* **Accessing the front-end:** geo-fencing blocks visitors located in restricted jurisdictions.

Withdrawals are never checked. An LP can exit using only the shares they hold, with no permission standing in between. A compliance layer that could freeze funds would in practice be acting as a custodian.

## Enforcement tools

| Tool | Description |
| --- | --- |
| Attestations instead of allowlists | A wallet receives an EAS attestation from the KYC provider once it clears identity, sanctions and residency screening. The attestation carries just three fields (role, jurisdiction class, expiry) and nothing personal. |
| Ongoing re-screening | Each attestation expires and must be renewed. Revoked credentials stop working the next time the holder tries to act. |
| Two separate screening layers | Screening by the KYC provider happens twice: on first issue and on every renewal. Independently, the sequencer performs sanctions screening with TRM Labs built in at the chain level. |
| Replaceable policy | The registry is built on adapters. Through the timelock, either piece can be exchanged with no redeployment: the policy engine (EAS, ONCHAINID, Chainlink ACE) or the roster of accepted issuers. That lets Canopic respond quickly when a regulator or the issuer calls for stricter rules. |
| Public reads | All view functions remain open, so aggregators and explorers can index the protocol without restriction. |

## Outside the protocol's role

* Custody of user funds, apart from the instant of atomic settlement and the LP-owned vaults.
* Fiat on-ramps or off-ramps of any kind.
* Influencing how any individual fill turns out. Quotes are computed from public state and settlement is verified by signature, so no one at Canopic can improve, worsen or block a specific trade.

These limits keep the regulatory footprint narrow. The front-end is the point where users meet the operating company, and wherever authorisation is legally needed, it is that company that gets assessed. The protocol remains non-custodial, non-discretionary software.

## Handling personal data

Personal data stays with the KYC provider and is never put on-chain. There is also an optional zero-knowledge credential route, which lets a wallet prove eligibility without revealing which provider checked it or the value of any underlying attribute.

## Following the issuer's lead

Canopic's access rules are tied to the Stock Token eligibility list. Any restriction Robinhood adds to that list is matched by the attestation issuer. Should Robinhood later introduce on-chain transfer hooks for Stock Tokens through ERC-7943 or ERC-3643, Canopic's vaults and settlement contracts will respect them and, in return, be allowlisted. See [Next asset classes](../assets/next-asset-classes.md).
