---
name: reviewing-extensions
description: Review an x402 extension spec or SDK. Use when a PR touches extensions packages, specs/extensions, or docs/extensions, including bazaar, sign-in-with-x, payment-identifier, gas sponsorship, or builder-code.
---

# Reviewing an extension

Use this for extension specs and packages. Authoring steps are in [contributing: new extension](../contributing/references/new-extension.md). Spec wording is also [reviewing-specs](../reviewing-specs/SKILL.md).

Carson will not merge an extension implementation until the spec file exists and a client plus server example runs (#921). Philippe checks that extension data has one shape across versions and that hooks do not corrupt `extra`.

## What they check

- Spec file under `specs/extensions/` before or with the first implementation. It is the source of truth for the other languages (#921).
- A runnable client and server example, and a second run that shows the extension's real effect (for sign-in-with-x, the second call is unpaid while the signature is valid) (#921).
- Multi-chain servers. The spec must not freeze a single auth configuration when EVM and SVM are both advertised (#921).
- Do not assume an SVM payload has a `payer` field. It is `{ transaction }`. Read the payer from the settlement result (#921).
- `required` is part of the extension info schema when a server may enforce the field, and both the resource server and the facilitator are documented as consumers (#1053).
- Normative expiration and domain checks use MUST. A receipt a third party verifies includes every input needed to rebuild the domain, including `network` (#935).
- Extension-specific data goes in `extensions`, not in `accepts` (#935).
- Package boundaries. `@x402/extensions` does not depend on viem; chain types stay in the mechanism package (#1328). Directory names are kebab-case, matching the import path (#1328).
- Startup validation runs even when the app registers the extension itself, and it warns on a missing `info` or `scheme` (#2481). Call the core helper (`checkIfBazaarNeeded`) instead of a private copy (#2481).
- Hooks copy `extra` deeply (`structuredClone`). A shallow spread lets one extension mutate another's nested object. Decide first-wins versus last-wins when two hooks write `payTo`, and pass the enriched extensions downstream (#2109).
- Discovery or bazaar records use one v2 shape. Do not leave v1 `discoveryInfo` beside a different v2 object without a migration (#2496).

## What they block on

- Implementation with no spec, or spec that only supports one network on a multi-chain server (#921).
- A `required` flag the server cannot actually enforce (#1053).
- Reading `/dist` to import a JSON Schema draft. Find a public entry, or pin an older draft on purpose (#774).
- v1-only behavior quietly copied into the v2 extension (#1328, gas sponsorship until its spec is v2).
- Application idempotency added inside a scheme when it belongs in an extension (#3164).

## What they accept

- An example-quality facilitator that is intentionally not atomic, as long as the spec still tells production facilitators to bundle (#1487). Carson treats that as an e2e shortcut, not the product.
- Hook storage interfaces shipped in the extension package so examples stay thin (#921, Philippe's `SIWxStorage` suggestion).
- Optional client-supplied builder codes, once facilitator and client ids are different fields and the facilitator checks the echo (#2050).
- A follow-up for directory renames when the new package already matches existing kebab-case extensions (#1328).

## Signals

- #921 sign-in-with-x: spec file, runnable examples, multi-network split, no SVM `payer`.
- #1053 payment-identifier: `required` in info and in the schema's required array.
- #935 offer and receipt: MUST/SHOULD, `network` on the receipt, leave `accepts` alone.
- #2481 bazaar startup: validate every registration path; warn on a bad shape.
- #2109 extension hooks: deep copy, aligned verify/settle context types.
- #1328 ERC-20 gas sponsorship: no viem dependency from the extensions package.
- #774 bazaar Ajv import: do not import from `/dist`.
