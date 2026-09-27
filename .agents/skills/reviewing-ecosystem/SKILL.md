---
name: reviewing-ecosystem
description: Review an x402.org ecosystem partner or facilitator listing. Use when a PR edits partners-data, ecosystem logos, or facilitator cards on the website.
---

# Reviewing an ecosystem listing

Philippe reviews these; Carson almost never leaves a comment on them. The bar is that the card renders and the metadata is true. A broken JSON file is a reject, not a nit, because the whole directory fails to parse.

## What they check

- `metadata.json` parses. A stray character means the entry will not render. Ask for a new PR that deletes it rather than patching around a broken file (#910).
- Category matches the directory README. An infrastructure partner does not belong under a product category if `Services/Endpoints` is the fit (#526).
- `discovery` is true only when the discovery URL returns resources. A 404 means `false` (#1289).
- The logo renders. Ask the author to run `typescript/site` locally and look at the card (#1082).
- Facilitator cards that grow an address field stay optional for partners that do not have one (#1034).
- Claims about trustlessness stay honest. Approving a listing is not an endorsement of a fee design that is still an open issue (#899, pointing at the facilitator-fee discussion).

## What they block on

- Invalid JSON (#910).
- `discovery: true` with a dead endpoint (#1289).
- A logo that was never checked in the site (#1082).
- The wrong category when the site docs already name the right one (#526).

## What they accept

- A clean metadata PR with no further product review. Many of these are approved with no written comments.
- Address fields as optional data (#1034).
- Merges by another maintainer after Philippe's comment is fixed. #910 was merged by `Must-be-Ash` after the JSON note. The review still stands: do not merge the broken file.

## Signals

- #910 Kobaru: stray character, file does not parse.
- #526 SLAMai: use the Services/Endpoints category.
- #1289 Algorand resources: discovery URL 404, set the flag false.
- #1082 auor.io: run the site and check the logo.
- #1034 facilitator addresses: optional field, then approve.
- #899: approval plus a pointer at the open trustless-fee issue, not a block.
