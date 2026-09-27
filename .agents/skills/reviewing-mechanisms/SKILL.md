---
name: reviewing-mechanisms
description: Review a new or changed x402 payment mechanism, scheme implementation, or settlement contract. Use when a PR touches mechanisms/ or contracts/, including a new chain's exact scheme or an upto/batch-settlement facilitator.
---

# Reviewing a mechanism

Use this when the diff implements a scheme under `typescript/packages/mechanisms/`, `go/mechanisms/`, `python/.../mechanisms/`, or `contracts/`. The spec, if present, is reviewed with [reviewing-specs](../reviewing-specs/SKILL.md). The implementation checklist for authors is [contributing: new mechanism](../contributing/references/new-mechanism.md). This skill is what reviewers actually enforced on merged PRs.

The reference is EVM, then SVM. A new chain is expected to look like those, not to invent a parallel API.

## What they check

- A merged spec exists and the code follows it. Facilitator verification is the security boundary.
- One language in the PR. Cross-check the other SDK instead of re-deriving behavior (#3141, #3115).
- Registration matches the reference. Server `register` returns `x402ResourceServer`, not the bare scheme. Client registration can take policies (#966).
- Facilitators can use more than one signer and pick one. Prefer announcing `extra.feePayer` (the SVM shape) over a boolean sponsorship flag (#711, #966).
- The client cannot set an unbounded fee the facilitator pays. Cap it, as SVM does (#711).
- `"$0.10"` default assets are stablecoins only. Do not map dollar pricing to the chain's volatile token (#966). Chains without a canonical stablecoin omit the default-asset file (#799).
- `invalidReason` is a stable machine-readable code. Human detail goes in `invalidMessage` (#2801).
- `settlement_pending` means the transaction was broadcast and the receipt wait failed. A reverted or otherwise terminal receipt is not pending (#3214). Cache only that pending case, and still return success if deleting the cache entry throws after confirmation (#3214).
- A fallback verification path must not hide a real failure. An amount mismatch should stay an amount mismatch, not become a smart-wallet error (#1527).
- Duplicate or already-open channels get their own reasons (`duplicate_settlement`, channel already open), and a failed settle deletes its in-flight cache entry (#3094).
- Lifecycle matches across languages. Go `Stop()` waits for the in-flight cleanup; TypeScript `stop()` has to as well (#3141). MCP and HTTP must call the same extension validation and after-execution hooks (#3115).
- On a settlement contract: the client never signs an amount it cannot know ahead of time; the server cannot swap the client signer in a way that traps funds; after the grace period, finalize is callable by anyone (#1950).

## What they block on

- Interface drift from EVM/SVM (return type, fee-payer field, policy hook) without a reason (#966, #711).
- Uncapped facilitator-paid fees (#711).
- Dollar pricing pointed at a non-stablecoin (#966).
- Treating a reverted transaction as `settlement_pending` (#3214).
- Scheme-local application idempotency when a scheme-agnostic extension already covers that (#3164).
- Client config where `0` means "omit" on one scheme and "use zero" on another. Hardcode the default instead of adding a confusing override (#3155, #3141).
- Code changes in a PR whose title is a doc fix (#2635).

## What they accept

- A thorough new chain (tests, examples, site, e2e) approved and published, with website facilitator support as a follow-up (#711, #966).
- An extra RPC balance check left optional when it is unclear the error string is worth the round trip (#711).
- A second opinion instead of a block, when the reviewer does not know the chain and the shape matches SVM (#792).
- Hardcoded compute-budget defaults shared with `exact`, rather than a new config type used by only one scheme (#3155).
- Philippe comments, then approves after the unused fields or the stray refactor are removed (#2697, #2635).

## Signals

- #966 Aptos and #711 Stellar: multi-address facilitator, `feePayer`, fee cap, register return type, stablecoin defaults, spec fallback for ledger time.
- #2801 XRPL: per-`assetTransferMethod` tests, stable `invalidReason`, require a real issuer before comparing.
- #3214 settlement pending: pending is timeout-only; do not cache reverts; run post-settlement verify on the smart-wallet path too.
- #3094 / #3164 SVM `upto` and batch-settlement: one success per deposit, roll back duplicate keys on RPC failure, match accepts by network not by the first row, rent cleanup must be able to abandon idle channels.
- #1950 batch-settlement contract: no server-chosen client signer, no redundant `receiver`, questions about 2771 versus EIP-712 are questions, not automatic blocks.
- #2389 Concordium: `native` (or a namespace), never an empty string, for the default asset.
- #799 default assets: the map exists for the `"$"` syntax, not as a gate on whether the network works.
