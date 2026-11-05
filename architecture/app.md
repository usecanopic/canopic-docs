---
description: A tour of Canopic's own interface at usecanopic.com/platform, covering its six pages, what each shows, and the design rules behind them.
---

# The Canopic app

The reference client lives at [usecanopic.com/platform](https://usecanopic.com/platform) and is built with Next.js. Under the hood it calls nothing but the public [API](api-and-sdk.md) and the contracts, exactly as an outside integrator would. No keys and no funds ever flow through it, and it has no privileged route to anything. Its purpose is narrow: to make mechanics that are already fair easy to read.

## Pages

The sidebar groups six destinations:

| Group | Page | Path | What it covers |
| --- | --- | --- | --- |
| Venue | Dashboard | `/platform` | Listed markets priced from a guarded Chainlink mid, the session currently in force, the spread it implies and the fee on top |
| Venue | Trade | `/platform/swap` | The swap ticket |
| Venue | Vaults | `/platform/liquidity` | Market vaults, their tiers and the LP side of the venue |
| Personal | Holdings | `/platform/portfolio` | Positions held by the connected wallet |
| Personal | Account | `/platform/settings` | Your display name and the roles this wallet is cleared for |
| Open record | Ledger | `/platform/explorer` | The public trade record, the parameters in force and the history of governance changes |

## The Trade ticket

Trade is the heart of the app. Instead of hiding [the price formula](../trading/price-formula.md), the ticket lays it out term by term:

| Part of the ticket | What it tells the trader |
| --- | --- |
| Price terms | A labelled line apiece for mid, spread and fee, shown in USDG and in bps, each tied back to its oracle round |
| Market hours badge | Sits right on the ticket and reads along the lines of "US markets closed. Weekend spread ×3 applies." That way the session is known before the trade, not after it. |
| Rate and expiry | An all-in rate protected by the slippage limit, plus a countdown to the moment the RFQ quote expires |
| Two-leg swaps | Every leg itemised on its own |

Once a trade settles, its receipt reproduces the breakdown stored on-chain and links out to Blockscout. Since the event carries the identical decomposition, any fill can demonstrate that the price charged matches the price quoted.

## Onboarding

To get in, bring an existing wallet such as Robinhood Wallet or MetaMask (any standard wallet will do). The other option is a passkey-secured embedded smart account created in-app with Alchemy Account Kit. The eligibility step guides users through attestation with the KYC provider and displays their status before asking for any signature, so an ineligible user reads the applicable rule rather than hitting a revert. On a newcomer's first trade, approval (via Permit2) and execution travel together in one sponsored user operation.

## Holdings

Holdings centres on the connected wallet and its listed positions:

* balances expressed both as tokens and as shares, using `uiMultiplier()`,
* prices marked against the guarded oracles the vaults themselves rely on,
* realised PnL and cost basis, worked out from the wallet's on-chain history,
* LP positions with value per share, spread income earned so far and the present inventory mix of each vault.

## Pages open to everyone

The [Ledger](../walkthroughs/use-the-ledger.md) and the [execution quality](../transparency/execution-quality.md) figures sit inside the platform and need no wallet to view. A public record has to be public, so nothing about the venue's history hides behind a sign-in.

## Design standard

[Design tenets](../basics/design-tenets.md) set the bar: calm, technical, exact. In practice:

1. **Figures.** Numbers are set in tabular numerals, no column mixes bps with USDG, and times show in the reader's local zone, with UTC on hover.
2. **Empty, loading and error states.** Whenever there is no data, data is on its way, or an action fails, the page explains it in plain language and suggests what to do next.
3. **Failing transparently.** If the quote service is down, the app falls back to vault-only quotes from the RPC and tells you. If the RPC is down, it says that too, instead of spinning forever.
4. **No manipulative design.** No slippage preset exceeds the default, nothing manufactures urgency, and no fee is hidden. Nothing about how the venue earns money needs to be obscured.

## Hosting it yourself

The app's source is public. Point `pnpm dev` at an RPC endpoint of your choice plus the public API and you get our hosted experience in full. KYC is the only piece that differs: plug in whichever attestation issuer the registry has approved.
