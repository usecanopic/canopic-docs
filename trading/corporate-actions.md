---
description: How Canopic treats dividends, splits, mergers and delistings so that no value slips out of a vault along the way.
---

# Corporate actions

Corporate actions are a common place for a tokenized stock venue to lose money quietly. Picture a 10-for-1 split: the token's price resets overnight, and any pool that was not informed gives the gap away to the first arbitrageur who spots it. Canopic avoids most of this by design, through the way the token and its oracle work. Anything left over is handled by a halt.

## Summary table

| Event | During the event | After the event |
| --- | --- | --- |
| Dividend | Nothing to do: multiplier and price move up in step | Nothing to do |
| Stock split, forward or reverse | The market sits in `HALTED` for as long as `oraclePaused()` is set | Quotes restart from the adjusted feed, and vault inventory stays as it was |
| Merger or acquisition | `HALTED` | Quotes restart, or, if the token is being wound down, the market moves to [retirement](#retiring-a-market-whose-underlying-disappears) |
| Delisting of the underlying | `HALTED` | The market is retired |

## Dividends: handled by the token itself

Robinhood's Stock Tokens implement ERC-8056. That standard exposes `uiMultiplier()`, which reports how many underlying shares a single token represents. When a dividend is paid, the multiplier goes up; no separate payout is sent. Chainlink's price already includes the multiplier. For a trading venue, every result of this is helpful:

* There is never a dividend token, so nobody has anything to claim, sweep, or split between LPs and traders.
* Value builds up gradually in both the token and the feed, so there is no ex-dividend price gap for a vault to get wrong.
* Nowhere in vault accounting is there special handling for dividends. For an LP, a rising multiplier looks like any other price change in the value of their shares.

Because the Ledger reads `uiMultiplier()` next to the price, it can display a position in share terms as well as in token terms.

## Other events: pause, absorb, resume

Each of these events runs through one fixed pattern.

**Pause.** Chainlink marks the feed with `oraclePaused()` whenever a split, merger, ticker change or similar event takes place. The market then moves into [`HALTED`](market-hours.md) automatically, with no human step. Vault quoting stops and RFQ fills no longer settle in that market. Deposits and withdrawals keep working as usual.

**Absorb.** When the feed resumes, the event is already reflected in both the price and the multiplier it reports. The vault's token balance is unchanged, so nothing in its inventory has to be corrected. A split changes the figure the feed reports, never the balance held by the contract.

**Resume.** Vault quotes restart from the adjusted value.

## Retiring a market whose underlying disappears

A market enters `RETIRED` through the timelock in two situations: its underlying no longer exists, or the issuer discontinues the token. From that point quoting is over for good, and withdrawals are the only action left. Each LP takes out a pro rata share of the inventory in kind and can then go through the issuer's redemption process if they choose. The protocol never holds a position through a wind-down on anyone's behalf. Like every other parameter change, each retirement is recorded in the governance log together with its rationale.

## Remaining risk

All of the above relies on the feed pausing before stale prices can be traded against. In practice, `oraclePaused()` is exactly what Chainlink's equity feeds use when a corporate action happens. Should a feed simply go quiet without pausing, the [oracle safeguards](../risk/oracle-safeguards.md) still step in, because a lack of updates is enough on its own to halt the market.

What remains is a feed that continues to publish wrong prices with confidence through an event. That is the oracle risk already present in every trade, and the [price formula band](price-formula.md) caps the loss on a single fill at the band's width.
