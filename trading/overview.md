---
description: A walk through a single trade, from the first price check to the moment it settles on-chain.
---

# The trade path, end to end

From the trader's side there is exactly one door. Behind that door, the work is split across three parts:

* **Market vaults** that take their price from the oracle and serve everyday order sizes.
* **A request-for-quote (RFQ) path** reserved for block-sized orders.
* **A router** whose job is to verify eligibility first and then settle with the source paying the trader more.

## Following a single trade

| Step | What takes place |
| --- | --- |
| Getting a price | Imagine someone asks what 2,000 USDG of tokenized NVDA would cost. The quote service runs the same logic the router will later run on-chain: it pulls the guarded Chainlink mid from `OracleAdapter`, checks which [market hours mode](market-hours.md) applies, and looks at the inventory held by the [market vault](market-vaults.md). When the order is big enough, it also gathers [signed maker quotes](large-orders.md) through RFQ. The answer is one price, broken down into mid, spread and fee. |
| Signing | One signature is enough. What gets signed is a `SwapRouter` call bundling the trade terms, a deadline and a worst acceptable price, together with a Permit2 transfer of whatever the user is selling. |
| Checks on-chain | From here the chain takes over. First the `TRADER` attestation is checked. Next the router derives the vault price independently from oracle and vault state; on the RFQ path it checks the maker's EIP-712 signature instead. Last, it confirms the result respects two ceilings: the limit the trader chose and the protocol's oracle band. |
| Settling | All transfers execute at once. The trader sends the input, the vault or maker sends the output, and the fee goes to the `ProtocolTreasury`. One transaction yields one fill event, visible on Blockscout a few seconds later. |

There is no order book to maintain, and traders never deposit first or withdraw afterwards. Funds sit with the protocol only for the duration of a single transaction, and only inside immutable contracts.

## Trading one listed asset for another

Prices in all Canopic markets are denominated in USDG. That means a trade between two listed assets (tokenized AAPL into tokenized SPY, for example, or a Stock Token into tokenized gold once gold has a market) runs as two USDG legs, both executed within one atomic router call. The trader sees a single price, and the fill event records the two legs separately. A leg that fails its band check takes the other leg down with it, and nothing settles.

## How orders are assigned to a source

* **Sizes within the vault clip** are filled by the market vault. There is no counterparty to wait on, and a formula sets the spread.
* **Sizes above the clip** are sent to RFQ, because a maker hedging on the underlying market can price large size more tightly than a fixed formula.
* **Any size where a maker offers a better price** also goes to RFQ. Whichever price is better always wins, and makers can beat the vault at any time.

The clip, then, is a guide rather than a wall. A maker can quote well below it and take retail flow every time its price is better than the vault's.

## Deliberate omissions

* **Assets never share a pool.** Each vault holds one asset plus USDG and nothing more, so LPs pick their exposure market by market.
* **People cannot touch fills.** Vault prices are the output of a calculation that anyone can reproduce from public data, and the RFQ path only checks a signature. No one at Canopic is able to help or hinder any individual fill.
* **Settlement is never deferred or netted.** Once a trade lands in a block it is final, with nothing left to settle later.

## Reading order for this section

The pages below start at the core and work outwards:

1. [Market vaults](market-vaults.md): how a vault holds its inventory.
2. [The price formula](price-formula.md): the maths behind every price.
3. [Market hours and pricing modes](market-hours.md): trading sessions, plus when markets halt.
4. [Large orders and signed maker quotes](large-orders.md): block-sized trades.
5. [How the router picks a venue](routing.md): how the parts are wired together.
6. [Providing liquidity to a vault](providing-liquidity.md): the picture for LPs.
7. [Corporate actions](corporate-actions.md): dividends, splits and similar events.
8. [Fees and spreads](fees.md): what you pay.
