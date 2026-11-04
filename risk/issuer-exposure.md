---
description: Behind each tokenized security stands an issuer. Here is what that counterparty means and how Canopic manages it.
---

# Issuer exposure

A Stock Token gives you a claim, not a share. In legal terms, Robinhood Assets (Jersey) Ltd is the issuer and the instrument is debt in token form, linked to a US share kept by a custodian. That distinction applies to every position traded or held on Canopic, so we handle it as a risk in its own right.

## Where the issuer comes into play

1. **Credit.** In an issuer insolvency, holders become creditors with a claim on the shares in custody. They might recover only part of their value, and recovery might take a long time.
2. **Freezes and restrictions.** Under its terms, the issuer may in certain situations freeze, suspend or restrict tokens. If it did, trader balances and vault inventory alike could be locked.
3. **Redemption.** Only KYC'd holders can turn tokens back in to the issuer. Canopic's design never needs that step, because its prices come from the oracle's view of the secondary market.
4. **Regulatory action.** Supervisors might order new terms, a withdrawal from some countries, or an end to issuance altogether.

## Canopic's approach

### Disclosure before anyone trades

On each listing page you will find who the issuer is, what kind of legal instrument the token is, and what our bytecode review concluded. Before a first swap or a first deposit, traders and LPs see a plain-English summary of how the debt claim works. No venue can engineer issuer risk away, but a venue can make sure nobody holds it without knowing.

### Reviewing the token's code

Robinhood states that its Stock Tokens cannot be frozen on-chain. We check that claim ourselves instead of taking it on trust. Before a market launches, a reviewer reads the token's deployed code and looks for four privileged powers: freezing, pausing, blacklisting and forced transfers. The findings go on the market's listing page, and any power that is present affects the market's tier and caps.

### Attestations of reserves

Some markets have a Chainlink Proof-of-Reserve feed (or an equivalent attestation) for the shares held in custody. In that case `OracleAdapter` consumes it and the listing page puts it alongside the market. When there is none, the listing page says that too.

### Caps and a single concentration figure

Volume and TVL caps bound how much exposure each market can accumulate. Because one issuer stands behind every Stock Token, the [Ledger](../walkthroughs/use-the-ledger.md) also shows one headline number: the aggregate inventory, across every vault, that rests on Robinhood Assets (Jersey) Ltd. We state the concentration directly so that no one has to calculate it.

### Isolation between markets

Each market is fully self-contained. A problem at the issuer, whether it hits a single Stock Token or the whole set, cannot reach a market backed by gold or bridged treasuries. Adding further issuers is a stated aim on [Next asset classes](../assets/next-asset-classes.md).

## For traders and LPs

Holding a Stock Token in your own wallet and holding it through a vault share come to the same thing: a claim on the custody arrangements of a regulated broker, not ownership of the tracked share. Should the issuer fail, the contracts would still work as intended and in-kind withdrawals would still go through. What you withdrew, however, would be that claim, and its value would become a legal question rather than a market one. Canopic limits this exposure with caps and disclosure. You may reasonably choose to be more cautious still.
