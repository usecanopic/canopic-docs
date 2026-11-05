---
description: A reference for each protocol contract, the one task it performs, and which parts of it can ever be adjusted.
---

# On-chain components

The contract count is small on purpose. Every contract performs a single task, its code is locked for the lifetime of that version, and any value that may need tuning lives in `SettingsTimelock`, guarded by the timelock.

## Contracts and their jobs

| Contract | Job | Mutable? |
| --- | --- | --- |
| `SwapRouter` | The sole entry point for traders. Front door for every trade: runs the eligibility check, chooses where to route, stitches together two-leg swaps, then holds each fill inside the band and inside the limit the trader signed | No |
| `OracleVault` (one per market) | Custodies one market's inventory, posts bids and offers around the mid, and creates or retires LP shares on deposit and withdrawal | The code is locked; parameters sit in `SettingsTimelock` |
| `VaultFactory` | Factory that spins up a vault for every listed token; the router consults it as its directory of markets | No |
| `RfqSettler` | Settles RFQ trades: confirms the EIP-712 signature on a maker quote, then executes the top-ranked quote in one atomic step | No |
| `OracleAdapter` | Price authority for the protocol. It applies guard checks to Chainlink data (both feeds and streams) and keeps track of the current session and regime | The code is locked; governance configures the feeds |
| `EligibilityRegistry` | Permission authority for the protocol, answering role questions by consulting attestations | The code is locked; `SettingsTimelock` selects the policy adapter |
| `SettingsTimelock` | Home of every tunable setting (each gated by the timelock) and of the guardian's address | Changes are timelocked; anyone may read |
| `ProtocolTreasury` | Collects protocol fees | No |

You will not find a dedicated guardian contract. Guardian status is just a field in `SettingsTimelock` naming an address; that address can switch on the emergency swap pause and has no other ability. Lifting that pause is beyond it, as are moving funds and obstructing withdrawals.

## Functions integrators call

```solidity
// SwapRouter: the single call a trader makes
function swapExactIn(SwapParams calldata p) external returns (uint256 amountOut);
function previewExactIn(SwapParams calldata p) external view returns (uint256 amountOut, Types.Venue venue);
struct SwapParams {
    address tokenIn; address tokenOut;
    uint256 amountIn; uint256 minAmountOut;
    address to;              // zero means the trader receives the output
    uint256 deadline;
    Types.MakerQuote quote;  // optional RFQ candidate for single-leg swaps, checked on-chain
    bytes quoteSig;
}

// OracleVault: what LPs call
function deposit(uint256 quoteAmount, uint256 tokenAmount, uint256 minShares, address receiver) external returns (uint256 shares);
function withdraw(uint256 shares, address receiver) external returns (uint256 quoteOut, uint256 tokenOut);
function quoteSwap(bool buyToken, uint256 amountIn) external view returns (uint256 amountOut, Types.Breakdown memory breakdown);

// OracleAdapter: public state
function quote(address token) external view returns (IOracleAdapter.Quote memory);
```

Each price is itemised by `Types.Breakdown`: four components (mid, half-spread, skew, fee) plus a session label. Because every fill emits this struct, the chain keeps exactly the same itemisation that appeared on the trader's ticket.

## Events on record

* **Trades.** `SwapRouter` emits `Swapped`, naming the trader, the token pair, both amounts and the winning venue. Vault-filled orders add a second event, `VaultSwap`, recording the pricing components and the session that applied.
* **Settings changes.** `SettingsTimelock` first emits `OperationScheduled`, giving the operation id, the earliest moment it may execute and the rationale. On execution it emits `ParamChanged` with the new value.

Every figure in [Use the ledger](../walkthroughs/use-the-ledger.md) is derived from these events and from no other source.

## The case against proxies

No contract in the suite is proxied. Upgrading the protocol is done by standing up an entirely separate contract set. Liquidity moves across only when LPs themselves pull their assets out in kind and put them into the successor vaults. That makes upgrades more awkward. The payoff is a guarantee that never expires: the code holding your assets right now is identical to the code the auditors reviewed.

The single exception covers pluggable pieces. `SettingsTimelock` stores the eligibility adapter and the list of accepted attestation issuers, and governance configures oracle feeds inside `OracleAdapter`. Data products and identity standards turn over quickly, whereas the rules that move money ought to stay put. Hence the split: plug-ins can be swapped, settlement cannot.

## Roles with special powers

| Role | Held by | Powers |
| --- | --- | --- |
| Owner, in its capacity as timelock proposer | Foundation multisig | Queues parameter changes, publishing a rationale with each one, and carries them out after the delay expires |
| Guardian | Foundation multisig, lower quorum | Engages the swap pause; lifting it is reserved for the owner, which may do so immediately, skipping the timelock delay |
| Keeper functions | No one in particular, because they are open to every caller | Advance regimes and trigger halts |
| Any other role | No one | The contracts contain no additional privileged functions |

The pause never applies to deposits, withdrawals or the cancellation of RFQ quotes.
