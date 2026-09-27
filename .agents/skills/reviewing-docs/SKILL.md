---
name: reviewing-docs
description: Review x402 documentation and website copy. Use when a PR edits docs/, the marketing site, a README, or facilitator setup guidance, and the change is not an ecosystem partner listing.
---

# Reviewing docs

Most docs and Mintlify sync PRs are approved with no comment (#3584). Write something when the page teaches the wrong protocol version, a single chain, or the testnet facilitator as if it were production. Ecosystem listings use [reviewing-ecosystem](../reviewing-ecosystem/SKILL.md).

## What they check

- v2 header names. `PAYMENT-RESPONSE`, not `X-PAYMENT-RESPONSE` (#755). The same substitution applies inside READMEs and guides.
- No single-chain bias in a page that claims to be general. Mainnet setup must not read as "use Base" (#2637).
- The public `x402.org` facilitator is for testnet. Docs must say not to reuse it for mainnet routes (#2637). Philippe changed the requested wording to that, then approved.
- Comments and callouts sit next to the example they describe, not above an unrelated block (#2637).
- v2 docs do not document legacy trees. The docs workflow and `AGENTS.md` should point at `python/x402/` and should exclude `typescript/packages/legacy/`, `go/legacy`, and `python/legacy` (#925).
- A docs-update workflow should not rebuild the site on ecosystem or `typescript/site` edits. Those are not doc content (#925).
- Facilitator "production-grade" edits match the existing page pattern. Empty approval is normal when the only change is that status line. It is not normal when the page newly recommends the testnet facilitator for mainnet (#2637).
- Typos in headings and alt paths get a one-line suggestion, not a rewrite (#755, #1767).

## What they block on

- v1 header names presented as current (#755).
- Mainnet instructions that send the reader to `https://x402.org/facilitator` (#2637).
- Base-only wording on a multi-chain setup page (#2637).
- Legacy packages included in the v2 doc set (#925).

## What they accept

- Mintlify and copy PRs with no comment once the terminology is already v2.
- A suggested sentence in place of a paragraph, then approval (#1767, #2637).
- Changelog and migration narrative that stays open where non-chain networks do not have wallet-to-wallet settlement, as long as the blockchain case is concrete (#446). Carson wrote that distinction; do not force one identity scheme onto fiat networks.
- A one-line site typo approved with no comment once the page is already accurate (#2639).

## Signals

- #2637 facilitator setup: remove Base bias, testnet facilitator is not for mainnet, move the callout.
- #755 README: `PAYMENT-RESPONSE`, fix the broken image path and the "Paymen Payload" typo Carson flagged.
- #925 Mintlify migration: exclude site and ecosystem from the docs workflow; v2 docs skip legacy.
- #1767 website copy: small wording suggestion, not a new information architecture.
- #3584 Mintlify docs update: empty approval.
