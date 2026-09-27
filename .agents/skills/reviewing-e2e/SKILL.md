---
name: reviewing-e2e
description: Review an x402 end-to-end test or harness change. Use when a PR edits e2e/ clients, servers, or facilitators, or claims an e2e run as the test for a new mechanism.
---

# Reviewing e2e coverage

E2e is where these reviewers expect a new mechanism to prove the HTTP path, and where they refuse to spend testnet funds on every pull request. Unit tests stay in CI. Integration tests that broadcast transactions are opt-in.

## What they check

- A new `assetTransferMethod` has its own e2e route, the way EVM splits EIP-3009 and Permit2. XRPL sequence versus ticket is the same idea (#2801).
- The harness is allowed to be simple. Production facilitators still follow the spec (atomic bundles for gas sponsorship). The e2e facilitator does not have to (#1487).
- Failures found by the harness get fixed in the adapter, not papered over. The Next adapter was dropping base64 on `PAYMENT-RESPONSE` and the e2e run is what caught it (#269).
- Client binaries are not committed. Build in `setup.sh` when a binary helps (#1149).
- Python `uv run pytest` collects integration tests and skips them when `.env` is absent. That skip must stay. Do not make CI send real testnet transactions on every commit (#2023). Document the split so reviewers know a green unit run did not execute the paid path.
- Examples and e2e agree on the tool. Advanced server docs tell the reader to install with `pnpm` from the examples tree (#93). Same expectation here.
- Offline or local e2e is preferred for a new chain until a faucet and funded wallets exist. Philippe asked Concordium for a repo-level `e2e/` target and a documented faucet (#2390).

## What they block on

- Committing built clients (#1149).
- A single happy-path test when the scheme has two asset-transfer methods (#2801).
- Turning integration tests on in CI by default (#2023).
- An e2e-only shortcut described as if it were the spec. Say that facilitators should bundle, and that this harness does not (#1487).

## What they accept

- A deliberately simple facilitator in the harness, with the atomic path left to production (#1487).
- A follow-up for onchain e2e when the offline test and the faucet instructions are in place (#2390). Philippe merged Concordium once those landed, and queued minor release fixes as a fast-follow.
- Devex cleanups inside the runner (flags, setup) approved without a design debate (#281).

## Signals

- #2801 XRPL: one test config per asset-transfer method.
- #1487 permit2 e2e: simple harness, atomic settle stays a facilitator concern.
- #269 Next header encoding found by e2e.
- #2023 Python: unit tests in CI, integration tests gated on env.
- #1149: no e2e binaries in git.
- #2390 Concordium: repo-level e2e plus faucet docs before merge.
