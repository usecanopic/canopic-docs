---
description: What Robinhood Stock Tokens are, and the properties that shape how they behave on a trading venue.
---

# Stock Tokens

All launch markets on Canopic trade Robinhood Stock Tokens. They are the main reason the venue sits on Robinhood Chain: equities brought on-chain by a publicly listed broker, with one-to-one backing, transferable at any hour as standard ERC-20s, and with Chainlink pricing.

## Live markets

Tier A has five names, each a token tracking one of: SPY, QQQ, AAPL, MSFT, NVDA. How tiers are assigned, and what a token must meet before a market opens, is covered in [Listing policy](listing-policy.md).

## Token profile

| Property | Detail |
| --- | --- |
| Issuer | Robinhood Assets (Jersey) Ltd |
| Legal form | Tokenized **debt securities**, meaning a claim on the issuer that tracks the share rather than direct ownership of it |
| Collateral | One-to-one shares in US custody |
| Standard | ERC-20 with 18 decimals, plus ERC-8056 for corporate actions |
| Availability | Over 120 countries |
| Contract-level transfer limits | **None.** You will not find a freeze function, transfer hook or allowlist anywhere in the documentation. Eligibility is enforced by Robinhood in two places only: its app, and the KYC'd primary channel used to mint and redeem |
| Pricing | Chainlink, through 24/5 Data Feeds and through Data Streams for market status; prices already reflect the ERC-8056 multiplier |

## What these properties mean for a venue

**Plain ERC-20 mechanics.** No allowlisting is needed for a vault or for RFQ settlement to take custody of the tokens and send them on. Likewise, once a wallet is attested it can trade any of them, with nothing to configure token by token.

**Corporate actions are built in.** With ERC-8056, value accrues inside the token and its feed. There is nothing to process, and a vault never meets an ex-dividend drop it might misprice. See [Corporate actions](../trading/corporate-actions.md) for how this works.

**Prices arrive with session data.** One guarded source provides the mid and the current session together. Vault pricing and [Market hours and pricing modes](../trading/market-hours.md) both rely on that.

**Compliance is not enforced on-chain.** This has two sides. Any address can hold the tokens, even one that Robinhood's own checks would reject. Canopic therefore applies eligibility on its own side of the protocol boundary using the [eligibility registry](../architecture/eligibility.md), checking traders, LPs and makers on every action where it is needed.

If Stock Tokens were ever to adopt ERC-7943 (uRWA) or ERC-3643 hooks, the impact would be small. As a precaution, the vault and settlement contracts already call `canTransfer` and `canReceive`; the issuer would only have to allowlist them. More in [Next asset classes](next-asset-classes.md).

## Risks attached to every holding

Canopic treats both as real: each is disclosed, and each is priced in.

1. **Credit risk on the issuer.** A token holder is a creditor of Robinhood Assets (Jersey) Ltd. If the issuer failed, the token's value would depend on recoveries from custody rather than on the share's market price. LPs in a vault and direct holders carry exactly the same claim.
2. **Contractual freeze rights.** Under certain conditions, the issuer keeps the contractual right to suspend, freeze or restrict tokens. Robinhood's documentation lists no freeze function on-chain. Even so, before a market launches Canopic inspects the token's deployed bytecode for forced-transfer, blacklist, freeze and pause roles and posts the results on the listing page. Any role discovered affects the market's tier and its caps.

For further detail, read [Issuer exposure](../risk/issuer-exposure.md).
