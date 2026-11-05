---
description: Audits, fuzzed invariants, live alerting and a disclosure channel, the measures that guard Canopic's code and services.
---

# Security program

Much of Canopic's safety comes from its structure: the logic is immutable, very few functions carry privileges, and the band limits the blast radius of nearly any fault. The security program exists to keep testing that small remaining surface and to watch it constantly.

## Guarantees encoded as tests

Each guarantee the protocol offers is expressed as a property in the Foundry test suite, which CI runs on every commit and which is also fuzzed continuously. The properties:

1. **Band safety.** The fuzzer may wander into any state, regime or parameter mix; a fill beyond the oracle band must still be impossible.
2. **Share monotonicity.** Value per share can never fall because of a swap, and mint and burn rounding always goes in the vault's favour.
3. **Withdrawal liveness.** `withdraw` succeeds from any vault state, including `HALTED` and `RETIRED`, and even during a pause.
4. **Inventory bounds.** Inventory stays within the band no matter how much quoting happens.
5. **Conservation.** Nothing is created or lost in a fill: what the trader pays equals what the vault or maker and `ProtocolTreasury` receive.
6. **Decimals.** Handling of 6 versus 18 decimals remains correct at both minimum and maximum sizes.

Oracle failures are tested scenarios, not assumptions. For each of the following, the suite checks that the correct halt occurs: a round that has gone stale, a feed that is paused, disagreement between stream and feed, a 25% price jump, and a sequencer that restarts.

## External audits

Mainnet exposure stays within the guarded caps until two independent audits are finished. The first is an economic review covering the spread formula, how shares are accounted for, and how the band is enforced. The second spans every contract in the suite. Both reports are published unedited, exactly as delivered.

Should settlement logic ever need altering, that happens via a new deployment, reviewed separately. Parameter updates skip that step, and this is a large part of why behaviour was moved into parameters originally.

## Bug bounty

At mainnet a public bounty launches on an Immunefi-style platform, with its top tier set aside for anything that violates one of the properties above. Its scope reaches beyond the contracts to include the quote service and the SDK. Client software that could trick a user into signing a harmful transaction is a real attack and gets rewarded, even when the chain behaves perfectly.

## Key handling and alerts

| Topic | Approach |
| --- | --- |
| Keys for the timelock and guardian | Kept on hardware devices; every signer is named and every threshold is public. The guardian may engage the swap pause, but `SettingsTimelock` refuses any attempt by the guardian to disengage it. Lifting a pause is the owner's job alone; the foundation multisig may do it on the spot, with no timelock wait. A compromised guardian key can therefore cause refused quotes at most, and never losses. |
| Service keys | None of the services controls a key that can reach funds. The only hot keys belong to makers, who carry that risk themselves, and the band already caps their worst outcome. |
| Alerts | The on-call signer is paged in four situations: fills drifting toward the edge of the band, oracle disagreement, skew reaching its limit, and unusual fill patterns. |
| Runbooks | All incident runbooks begin by pausing quoting, since a pause can never cause harm. |

## Disclosing a vulnerability

Send reports to security@usecanopic.com, for which a PGP key is published. A public post-mortem follows any incident within 14 days at most. Any fix the incident calls for then goes through the timelock, with the post-mortem cited as its justification. A venue like this earns credibility only if [Our guarantees](../transparency/guarantees.md) hold up under stress, and this program is where that gets proven.
