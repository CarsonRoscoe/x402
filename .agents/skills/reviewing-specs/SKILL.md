---
name: reviewing-specs
description: Review an x402 specification change the way maintainers do. Use when a PR adds or edits specs/ (scheme, extension, or core spec), including facilitator verification rules and wire fields.
---

# Reviewing an x402 spec

Use this for `specs/` changes. If the same PR also lands a mechanism or extension implementation, review that with [reviewing-mechanisms](../reviewing-mechanisms/SKILL.md) or [reviewing-extensions](../reviewing-extensions/SKILL.md). Authoring rules live in [authoring-specs](../authoring-specs/SKILL.md). This skill is the review pass those authors should expect.

Carson and Philippe both treat the spec as the contract other SDKs copy. Philippe pushes unused fields out. Carson checks that a client can actually perform each step the spec requires, and he will not merge an implementation that has no spec (#921).

## What they check

- v2 names only. `PAYMENT-REQUIRED`, `PAYMENT-SIGNATURE`, `PAYMENT-RESPONSE`. Not `X-PAYMENT` or `X-PAYMENT-REQUIRED` (#732, #755).
- Scheme versus mechanism. The same payment flow can share a scheme and switch on `extra.assetTransferMethod`. A different flow is a different scheme (#769, #1425). Do not branch one spec on metadata that changes who holds funds.
- Normative keywords. Verification rules are MUST or SHOULD, not prose hints (#935).
- Every wire field is consumed. Drop fields the client or facilitator never reads. `decimals` and a non-canonical `channelProgram` came out of the SVM `upto` spec for that reason (#2697).
- Names match the sibling spec. Prefer `assetTransferMethod` (one string, not an array of profiles), `payTo` over a new `receiver`, and a shared `SettlementResponse` field such as `settledAmount` over a scheme-only response (#2697, #1074, #1950).
- Facilitator checks are explicit: amount equals `requirements.amount`, payload recipient is the collector, fee is capped, authorization entries cannot smuggle sub-invocations or custom-fee debits (#1425, #711, #792).
- The client can compute or verify every value the spec tells them to trust. A voucher the client adopts must be signature-checked, the way the EVM sibling spec says (#2698). A settlement buffer must have a size, or be labeled as facilitator policy rather than a contract rule (#2698).
- Spec table and SDK stay in sync. A `validation` flow that no scheme uses gets removed from both (#3053).
- Language stays network-neutral ("network ledger") so a non-chain settlement still fits (#3053).
- Default assets are named constants. An empty string must not mean native (#2389).
- Extension payloads live under `extensions`, not inside `accepts` (#935).

## What they block on

- v1 header or amount fields.
- A second settlement pattern hidden inside `upto` (streaming, time-based auth, multi-settle). That is another scheme (#1074).
- Two payment flows stuffed into one spec because they share a metadata flag (#1425).
- Unused or server-declared chain facts the facilitator should read itself (`decimals`, arbitrary `channelProgram`) (#2697).
- A missing client step that the sibling spec already requires (#2698).
- An implementation PR with no spec file (#921).

## What they accept

- A chain spec that follows the Solana paved road (`extra.feePayer`, partially signed transaction) even when the reviewer is not an expert on that chain, plus a request for a second opinion before merge (#792).
- A new shared field on the base spec when every scheme benefits (`settledAmount`, `minTimeoutSeconds`) and the author explains why (#1074).
- Wording nits that do not change trust, called out as nits, without holding the PR (#3053).
- In-spec discussion that ends with the field deleted once nobody can name a consumer (#3053, #2697).

## Signals

- #1074 (`upto` EVM): scope, where the actual amount is passed, `name`/`version` only if permit reconstruction needs them.
- #1425 (commerce / escrow): separate schemes when the fund flow differs; exact already covers "no charge if the server fails".
- #2697 (SVM `upto`): remove dead fields, canonical program, tx hash even when the charge is 0, rent payer must actually sign.
- #2698 (SVM batch-settlement): client verifies the voucher; do not invent an unquantified buffer.
- #2050 (builder-code): facilitator MUST check the client echoed the server's code; pick one id spelling.
- #935 (offer and receipt): MUST/SHOULD, include `network` if a third party must rebuild the domain.
- #1053 (payment-identifier): a `required` flag servers can enforce; document that both server and facilitator can read it.
