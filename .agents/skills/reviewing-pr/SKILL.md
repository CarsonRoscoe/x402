---
name: reviewing-pr
description: Triage an x402 pull request and choose which review skill applies. Use when starting a review, when a PR mixes spec, mechanism, extension, SDK, MCP, docs, examples, ecosystem, e2e, and CI changes, or when deciding whether legacy or release-only edits need a deeper pass.
---

# Triaging an x402 review

Start here. The category skills record how Carson Roscoe (`CarsonRoscoe`) and Philippe Dargent (`phdargen`) actually reviewed merged PRs. Apply every skill whose trigger matches. A new mechanism that also adds examples, an e2e test, and a publish workflow needs all four checklists, not the first hit.

Both reviewers approve a large share of merges with no written comments. An empty approval means the diff looked routine, not that the checklist was skipped. Written reviews cluster on specs, new mechanisms, extensions, and cross-language SDK behavior.

## Route

| The diff is mostly… | Skill |
| --- | --- |
| `specs/` prose, wire format, or facilitator verification rules | [reviewing-specs](../reviewing-specs/SKILL.md) |
| `*/mechanisms/*` or `contracts/` | [reviewing-mechanisms](../reviewing-mechanisms/SKILL.md) |
| `*/extensions/*`, `specs/extensions/`, or `docs/extensions/` | [reviewing-extensions](../reviewing-extensions/SKILL.md) |
| MCP client, server, or transport (`/mcp`, title mentions MCP) | [reviewing-mcp](../reviewing-mcp/SKILL.md) |
| `e2e/` | [reviewing-e2e](../reviewing-e2e/SKILL.md) |
| `examples/` | [reviewing-examples](../reviewing-examples/SKILL.md) |
| `docs/`, website copy, facilitator setup, README | [reviewing-docs](../reviewing-docs/SKILL.md) |
| Ecosystem partner metadata, logos, `partners-data` | [reviewing-ecosystem](../reviewing-ecosystem/SKILL.md) |
| `.github/`, publish workflows, version bumps, changelog policy | [reviewing-ci](../reviewing-ci/SKILL.md) |
| Any other SDK bugfix or core/http change | [reviewing-sdk](../reviewing-sdk/SKILL.md) |

Also follow [contributing](../contributing/SKILL.md) for repo-wide rules (one issue, v2 only, legacy frozen, changelog fragment, signed commits).

## No dedicated skill

- **Legacy** (`typescript/packages/legacy/`, `go/legacy`, `python/legacy`, `java/`). Merged reviews here do not form a checklist. Treat v1 as frozen; only a security patch belongs. See contributing.
- **Repo meta** such as a release tag script. #2675 was approved with no comments. Do not invent a process review for it.

## Always, before merge

- **Signed commits.** Carson blocks an otherwise acceptable PR until every commit is verified (#680, #788, #941). Say so in the review and wait; do not merge unsigned history.
- **Scope.** Philippe asks to revert code that the title does not claim (#2635). A docs typo does not need a refactor.
- **v2 vocabulary.** `PAYMENT-REQUIRED`, `PAYMENT-SIGNATURE`, `PAYMENT-RESPONSE`, `amount`, CAIP-2 network ids. `X-PAYMENT` and `maxAmount` are v1 (#732, #755).

## How they leave the review

- Philippe rarely uses changes-requested (merged examples: #924, #2635, #2637, #2697). He comments, then approves once the fix lands, and he merged most PRs he reviewed.
- Carson uses comment reviews rather than changes-requested. He withholds approval when a spec file, a runnable example, or commit signing is still missing (#921, #680).
- Nits are labeled as nits (#3155, #1487). Do not block a merge on one.
- If you do not know the chain, say so, compare the design to the EVM or SVM paved road, and ask for a second opinion instead of inventing rules (#792).
