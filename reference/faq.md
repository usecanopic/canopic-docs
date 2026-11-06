---
description: Straight answers to the questions traders, LPs and integrators ask most.
---

# FAQ

## The venue

**What kind of platform is Canopic?**
It is a non-custodial swap venue. Liquidity has two sources, oracle-anchored vaults and competing makers. Settlement is atomic and happens on Robinhood Chain. You never deposit into an account, and no resting order book exists.

**How are prices set?**
A single formula produces every price, and all of its inputs are public. The Chainlink mid is the base. On top of it sits the tier spread (times the regime multiplier), then an inventory skew adjustment, and lastly a fee shown as its own line. No one at Canopic has the ability to change a quote or a fill. Read [The price formula](../trading/price-formula.md).

**How can I verify that I got a fair fill?**
Trust is not required. The itemised breakdown of each fill is stored on-chain, and the distance between every fill and the reference price is published, unfiltered, in [Execution quality reports](../transparency/execution-quality.md).

**What happens if the company behind Canopic goes away?**
The protocol keeps working. Vault prices are computed from on-chain state, LPs can always withdraw, and the open-source services can be run by any third party. Read [Legal structure](../compliance/legal-structure.md).

**Can I build Canopic into my app?**
You can. Anyone may call the router without permission, the [API and SDK](../architecture/api-and-sdk.md) are public, and every integrator gets the same terms. Eligibility checks apply to each end user's wallet, so your app does not need to be approved.

## Trading

**If I buy tokenized AAPL, am I an Apple shareholder?**
You are not. What sits in your wallet is a Stock Token issued by Robinhood Assets (Jersey) Ltd. Legally it is an ERC-20 debt security tracking the share price, and dividends accrue inside it through its multiplier instead of being distributed to you. Before trading meaningful size, read [Stock Tokens](../assets/stock-tokens.md).

**What happens with dividends?**
They are absorbed into the token. The issuer reinvests each payout, which shows up as a higher `uiMultiplier()` on the token and a correspondingly higher price. You have nothing to claim. Read [Corporate actions](../trading/corporate-actions.md).

**Why is tonight's spread so much wider than this afternoon's?**
The asset's home exchange has closed. LPs now carry the risk of a gap at the next open, and the closed regime compensates them: spreads get a ×3 multiplier (three times wider) and the per-swap clip is reduced. The badge on the ticket shows the multiplier in force. Read [Market hours and pricing modes](../trading/market-hours.md).

**Can anyone trade during a halt?**
Nobody can, by design. Halts are driven by the oracle (stale data, corporate actions, implausible price jumps) and lift once the oracle is healthy again. Nothing leaves your wallet while a halt is in effect.

**Why was my trade rejected before I was asked to sign?**
Your wallet either lacks a valid `TRADER` attestation, or the asset is restricted where you live. The app tells you which rule applied. Read [Eligibility checks](../architecture/eligibility.md).

## Liquidity provision

**How do LPs earn?**
A vault retains the spread it earns on fills. Of that spread, 10% goes to the protocol; the other 90% accrues to vault shares and lifts their value. Nothing is emitted. LP income is just the price traders pay to be filled immediately, and the [ledger](../walkthroughs/use-the-ledger.md) shows each market's realised numbers.

**Can a halt or pause lock up my deposit?**
It cannot. Withdrawals are protected by a contract invariant, so you can leave in any vault state, a halt and the guardian pause among them. Exiting returns a pro rata cut of whatever the vault holds, based on your share count. Read [Providing liquidity to a vault](../trading/providing-liquidity.md).

**Do vault LPs suffer impermanent loss?**
Not in the way AMM LPs do. That loss comes from arbitrageurs trading a curve back to the reference price, a channel anchored vaults close off. Two risks remain yours: inventory risk, since the token itself may decline, and gap risk on anything filled while the regime is closed. We disclose both, and the spread is what pays you for carrying them.
