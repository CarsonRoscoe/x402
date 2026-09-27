---
name: reviewing-sdk
description: Review an x402 SDK bugfix or core/http change that is not a new mechanism, extension, or MCP transport. Use when a PR fixes facilitator, client, server, or HTTP middleware behavior, or when the diff should stay as narrow as its title.
---

# Reviewing an SDK change

Use this for bugfixes and core or HTTP middleware edits. New schemes go to [reviewing-mechanisms](../reviewing-mechanisms/SKILL.md), extensions to [reviewing-extensions](../reviewing-extensions/SKILL.md), MCP to [reviewing-mcp](../reviewing-mcp/SKILL.md). Most merged SDK reviews are empty approvals. The written ones are about scope, error codes, and not hardcoding USDC.

Philippe's merged changes-requested reviews on SDK diffs are specific and then re-approved (#924, #2635). Carson flags a concrete bug and asks for the smaller fix (#2556, #3274).

## What they check

- The diff matches the title. A docstring fix does not include drive-by code changes. Ask for those to be reverted (#2635).
- Behavior matches the other HTTP adapters. If Hono deletes a settlement-override header in place, Next and Express do too. Do not clone the whole `Response` when the headers are already mutable, and do not remove the header twice (#2556).
- Decimals come from the asset, not a hardcoded USDC `6`. Display strings should not pad every token to six fraction digits (#1926).
- Public imports. Do not read another package's `/dist` (#774).
- Error reasons stay specific. A fallback path must not replace `invalid_exact_svm_payload_amount_mismatch` with a generic smart-wallet error (#1527).
- Pending versus terminal settlement, including on the retry cache (#3214). See [reviewing-mechanisms](../reviewing-mechanisms/SKILL.md) when the fix is inside a scheme facilitator.
- Cross-language parity for the same hook or field. A TypeScript readonly `dynamicExtraFields` list and a Go path that stamps a fresh blockhash into `extra` need to be the same policy (#3115).
- Defaults that change caller behavior are called out. A new spend cap that silently limits existing clients is a breaking change, not a patch (#3124).
- Generated or local files (`next-env.d.ts`) are gitignored, not committed (#2556).

## What they block on

- Unrelated refactors in a fix PR (#2635).
- A line the previous PR was supposed to delete, still present (#924).
- Hardcoded USDC assumptions in shared display or parse code (#1926).
- Importing internals from `/dist` (#774).
- Making a root SDK API async-only when callers still need a sync wrapper. Callbacks may be async; the whole server should not have to be (#900). Carson asked for a sync class beside the async one rather than breaking portability.

## What they accept

- The narrow fix, approved as soon as the extra edits are gone (#2635).
- Making a field required when there was never a reason for it to be optional (`httpContext` on Next settlement overrides) (#2556).
- A small API addition that came out of review (`paymentStatus`) instead of overloading an existing field (#2558).
- Go being stricter than TypeScript about types, as long as the runtime behavior matches and a comment says why (#2575).
- Self-sends left as a facilitator hook (`beforeVerify` / `beforeSettle`), not a protocol rule (#1044).

## Signals

- #2635 signer docs: revert the code, keep the docstring.
- #2556 settlement overrides: mutate headers, do not rebuild the response, do not double-remove.
- #924 proxy headers: remove the leftover line, then approve.
- #1926 display amount: no fixed 6 decimals, trim trailing zeros.
- #774 Ajv: public import only.
- #900 Python HTTP server: async callbacks without forcing every caller onto async.
- #3124 spend controls: changing the default cap is a breaking upgrade; per-package changelogs for chains that do not implement `findDefaultAsset`.
