---
description: Which parties can alter Canopic's configuration, the path every change follows, and the firm boundaries on that authority.
---

# Changing protocol settings

All of Canopic's adjustable configuration is stored as values in `SettingsTimelock`. The list runs from pricing inputs (spreads, multipliers, fees) through size and risk limits (clips, caps, bands) to oracle adapters, the issuers trusted for eligibility, and which tier each listing sits in. Governance is the process for editing those values, and its scope was kept narrow on purpose. It can tune how the venue operates, and it can do nothing beyond that.

## Who controls it today

Governance currently sits with the foundation multisig, whose signers are named and whose thresholds are public (see [Legal structure](../compliance/legal-structure.md)). Once the planned governance module arrives, it receives an identical set of permissions and an identical set of prohibitions. The identity of the proposer could change over time; the scope of what can be proposed will not. Holding a say here earns no yield. Voting on a Canopic setting puts nothing on the line except the quality of the venue.

## The path of a change

1. **Proposal.** Every submission from the foundation multisig must spell out in writing the edit itself, its motivation, and the evidence for it; that evidence is usually a listing review or data from the [execution quality reports](execution-quality.md).
2. **Timelock.** The change cannot execute until its waiting period has elapsed, and the proposal remains publicly visible the entire time. Standard parameters wait 48 hours. The longer 7 day delay is reserved for the most sensitive levers: fee levels, the oracle adapters, and the issuers trusted for eligibility. Everyone affected therefore gets notice in advance, and an LP who disagrees is free to withdraw first, unconditionally.
3. **Execution.** On-chain application logs three things: the value before, the value after, and the proposal hash. The full history of every parameter on the venue is kept in the **Change history** on [the Ledger](../walkthroughs/use-the-ledger.md).

## Emergencies

Waiting 48 hours is not an option in the middle of a live incident, so the guardian can halt quoting and settlement straight away. The safety of this rests on an asymmetry. A pause takes effect immediately and harms nobody, since withdrawals keep functioning. The guardian cannot undo a pause; resuming is reserved for the owner multisig, which may unpause directly with no timelock delay. Substantive edits, by contrast, always take the timelock route. An incident note must accompany anything the guardian does; a pause lacking that note breaches [our guarantees](guarantees.md).

## Hard limits on governance

| Governance cannot | Why |
| --- | --- |
| Move or freeze anyone's funds | There is no contract function capable of it |
| Block or postpone withdrawals | An invariant keeps withdrawal open in all states |
| Modify settlement logic | Deployed settlement code is immutable, so changed behaviour can only arrive in a separate deployment users choose to move to |
| Apply fees retroactively | The fee is read at fill time and governs that fill alone |
| Bypass the band | The band check has no override |
| Make a substantive change immediately | Only the pause is instant; everything else waits out the timelock |
