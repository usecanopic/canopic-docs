---
description: The playbook Canopic follows if the underlying L2 stalls, censors or changes its rules.
---

# Chain outages and sequencer faults

Robinhood operates the lone sequencer of Robinhood Chain, an L2 rollup built with Arbitrum Nitro. That setup is common among L2s at this point in their development. It still brings specific ways to fail, and a venue open around the clock has to plan for them rather than hope they never happen.

## What can go wrong

* **Sequencer downtime.** No L2 transactions are included during the outage, while the home market for each asset keeps trading.
* **Sequencer censorship.** Particular transactions are left out, such as an LP's withdrawal or a maker's quote cancellation.
* **Slow L1 finality.** Withdrawing to Ethereum still takes the canonical 7 days, although trading on the L2 continues unaffected.
* **Changes to chain configuration.** A move to a different DA mode, a new validator set, or enabling Timeboost or BoLD would alter the ordering and finality assumptions Canopic builds on.

## Mitigations

### Sixty minutes of quiet once the sequencer returns

Alongside its price feeds, `OracleAdapter` consults the Chainlink L2 Sequencer Uptime Feed. If that feed shows the sequencer has only just recovered, all markets remain `HALTED` for a **one-hour** grace window. During the outage, prices kept moving, LPs had no way to withdraw, and no parameter could be changed. The hour lets oracle rounds catch up and lets everyone respond before a vault that froze mid-move can be traded against. Withdrawals and cancellations are available the moment the sequencer returns; only quoting waits.

### Forcing transactions in from Ethereum

Any Canopic function that changes state can also be submitted through Arbitrum's delayed inbox on Ethereum. If the sequencer leaves such a transaction out for 24 hours, it can be forced into the chain. In practice this means a censoring sequencer cannot stop:

* LP withdrawals,
* makers cancelling quotes by nonce,
* emergency pauses by the guardian, or settings updates queued in the timelock.

Swaps gain little from this path, because every quote will have expired well within 24 hours. The point is that none of the mechanisms that safeguard funds rely on a well-behaved sequencer.

### All timing uses `block.timestamp`

Arbitrum-stack chains return an L1-derived value for `block.number`. For that reason the timelock, grace windows, staleness bounds and quote expiries rely solely on `block.timestamp`. Where the true L2 block height is needed, which is rare, Canopic calls `ArbSys.arbBlockNumber()`.

### Minimising L1 data fees

Gas includes an L1 data component priced on compressed calldata size. To keep that small, RFQ quote structs are tightly packed and signatures use the 64-byte compact form whenever possible. The result: a settlement costs under a cent, blob price spikes included.

## Monitoring transaction ordering

Ordering today is first-come-first-served, with preconfirmations arriving after about 100 ms. We follow two potential upgrades carefully: Timeboost, which would auction an express ordering lane, and BoLD, which would make validation permissionless. Both have consequences for quote competition and for the forced inclusion path. Canopic never relies on favourable ordering: wherever in a block a fill lands, the oracle band and the signed slippage limit bound it. A change to the chain's configuration triggers a review, which we publish in the governance log.
