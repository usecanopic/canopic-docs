---
description: The asset classes planned after Stock Tokens, and the conditions each must meet before it lists.
---

# Next asset classes

New asset classes join Canopic in sequence, in the order that each can actually satisfy the listing requirements on Robinhood Chain. A class moves forward when the supporting infrastructure is in place, not when it has merely been announced.

## Never listed, whatever the demand

Certain assets are excluded at every stage:

* Wrapped assets such as receipt tokens or vault shares, where the value would have to be computed instead of read straight from a feed. We only price the precise token held, with no exceptions; the July 2026 wGOOGLx episode shows why, as covered in [Lessons from earlier tokenized venues](../risk/lessons.md).
* Synthetic exposure lacking both a regulated issuer and one-to-one backing.
* Any token whose deployed code reveals transfer restrictions that the issuer did not disclose.

## Stage one: Stock Tokens, live and growing

The Tier A markets launched first. Tier B and Tier C markets follow in batches, and each one waits until its feed is confirmed and its bytecode review is done. The long tail is where an RWA-focused venue proves its value: pooled designs tend to cap these assets tightly or reject them, and thin general-purpose pools misprice them worst. See [Listing policy](listing-policy.md).

## Stage two: tokenized US Treasuries

Among transferable RWAs, only stablecoins outsize tokenized US Treasuries, which sit at roughly $16B. They follow equities for that reason. A trader who sells down a stock position ought to find a yield-bearing asset right here on Canopic.

Before a treasury market can open, all of these must hold:

| Condition | Specifics |
| --- | --- |
| An eligible on-chain token | A regulated issuer's treasury token on Robinhood Chain, native or bridged over CCIP, and freely transferable in the way USDY is, not restricted to an allowlist |
| Pricing | A Chainlink NAV feed for that exact token, live on Robinhood Chain |
| Full requirements plus NAV rules | All items in the [listing requirements](listing-policy.md); NAV-based assets are quoted in a narrow band around NAV, and closed-session handling is replaced by the fund's own accrual, because treasuries do not have the weekend gap seen in equities |

If a token enforces transfer restrictions in its contract, the permissioned variant of the vault is used and the issuer allowlists it. Eligibility then gets checked twice, once in the issuer's own hook logic and again in the Canopic registry.

## Stage three: tokenized gold

PAXG-style gold trades continuously against a spot reference, with no sessions. Of anything on Canopic, it should be the easiest to run: always `OPEN`, with the spread shaped only by feed cadence and inventory.

The baseline is unchanged: the precise token must reach Robinhood Chain over a bridge and have a dedicated Chainlink feed there. Gold also needs a bridge review, which looks at three things: how tokens are minted, what the custodian attests to, and what happens if the bridge pauses.

## Later: further RWA classes as they reach Robinhood Chain

Robinhood has said the chain will host private-market tokens and other RWA types. Whatever arrives goes through the same checklist. An asset that fails it, for example because there is no token-specific feed, no session signal, or no disclosed issuer terms, will not be listed no matter how much demand there is.
