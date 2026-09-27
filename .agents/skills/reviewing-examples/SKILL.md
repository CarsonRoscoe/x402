---
name: reviewing-examples
description: Review an x402 example client, server, or facilitator. Use when a PR adds or edits examples/, including all-networks samples and the runnable demo that accompanies a new extension or chain.
---

# Reviewing examples

Examples are part of the review, not a nice-to-have. Carson has held an extension that looked correct until a spec file and a client/server pair existed (#921). Philippe reads the example for credentials the SDK does not actually support, and for env names that do not match the code.

## What they check

- The example runs with the tools the repo uses. Prerequisites list `pnpm`, and the README says to `pnpm install && pnpm build` from `examples/typescript` (or the matching language root), not from the example directory alone (#93, #94).
- Sync versus async is labeled as a comparison. Point people who want sync at the middleware (#93).
- A second run demonstrates the feature. Sign-in-with-x should show the first call paying and the next call unpaid, and it should print `PAYMENT-RESPONSE` when present (#921).
- The README matches the SDK. If Keeta accepts a mnemonic and not a raw private key, the sample and the env var say mnemonic (`KEETA_MNEMONIC`) everywhere (#2579).
- New chains are added to the all-networks client, server, and facilitator examples, not only to the package (#2390, #2579).
- Dollar amounts and asset names match the mechanism. Do not advertise `"$0.10"` on a chain whose default asset is not a stablecoin (#966).
- Faucet or testnet funding steps are real. Philippe tried the Keeta faucet path and the Concordium one and asked for native plus token instructions in the README (#2579, #2390).

## What they block on

- Merging a new extension with no runnable example (#921).
- Docs that tell the user to export a key type the signer cannot parse (#2579).
- A sample that skips `pnpm` or assumes a global install (#93, #94).
- Examples that still say `X-PAYMENT` (#755).

## What they accept

- The example living in a follow-up only when the mechanism PR already has unit coverage and the follow-up is named. Website facilitator registration was that kind of follow-up for Stellar (#711). The extension case is stricter: Carson wanted the example in the same PR (#921).
- Hook helpers moved into the extension package so the example stays short (#921).
- Advanced examples that show both the middleware path and a manual path, as long as the README says which one to copy (#93).

## Signals

- #93 and #94: prerequisites, `pnpm`, sync versus async callout.
- #921 sign-in-with-x: examples are a merge condition, including a second unpaid run.
- #2579 Keeta: mnemonic, env name, all-networks wiring, faucet that a reviewer can actually use.
- #2390 Concordium: all-networks plus funding instructions.
- #966 Aptos: do not treat a non-stablecoin as the `"$"` default in samples.
