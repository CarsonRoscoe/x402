---
name: reviewing-ci
description: Review x402 CI, publish workflows, changelog policy, and version bumps. Use when a PR edits .github/workflows, package publish config, changesets, or a release chore.
---

# Reviewing CI and releases

Publish workflows and version bumps are where a bad default ships to every package. Carson reviews the workflow mechanics. Philippe reviews changelog policy and whether a new package's workflow matches the latest template. Feature PRs that only add a changelog fragment are reviewed as SDK or mechanism changes; this skill is for the workflow and the release itself.

## What they check

- Node version matches what unit tests run. Do not publish on a Node release that is already end-of-life when CI is on 20 and 22 (#63).
- Install and build from the workspace root (`typescript/`), then filter. A package-local `pnpm install` does not build workspace dependencies (#63).
- One source for the pnpm version. `packageManager` in `package.json` wins over `version:` on `pnpm/action-setup`, because Vercel reads `packageManager`. If both are set, the action errors. Use `run_install: false` when the version comes from `packageManager` (#63).
- Actions are pinned by commit SHA when the neighboring workflows are (#63).
- A new scoped package copies the current publish workflow (`publish_npm_scoped_x402_evm.yml` is the template Philippe pointed at) and is listed wherever packages are released (#2579).
- TypeScript SDK versions move together. Major is the x402 protocol generation (`2`). Minor matches `@x402/core`. Patch floats per package (#711).
- Workspace ranges use `workspace:^`, not `workspace:*`. The star form becomes an exact version and blocks a patch bump. The caret form floats patch (#944).
- Changelog entries are skipped only for docs-only and ecosystem PRs. Internal changes still get a fragment. Do not widen the skip list in the PR template (#1036).
- Changesets target the package that changed. A shared changeset is wrong when Keeta, XRPL, and Concordium each need their own (#3124).

## What they block on

- Publishing from a stale Node, or building a single package without its workspace deps (#63).
- `workspace:*` on an internal dependency during a version bump (#944).
- A new mechanism package with a hand-rolled publish workflow (#2579).
- Changelog policy that hides internal SDK changes (#1036).
- Unsigned commits on the release PR. Same rule as every other PR (#680).

## What they accept

- A publish workflow that matches the current EVM (or other latest) template with only the package name changed (#2579).
- Version-bump PRs approved with no comment when the ranges are already `workspace:^` and the changelog fragments are already on main. Many `chore: version` merges look like that.
- Docs-only and ecosystem PRs with no changelog fragment (#1036).
- Shipping the workflow first and the hosted-facilitator package in the same PR once it is actually required, rather than blocking on a package nobody can call yet (#63).

## Signals

- #63 npm publish: Node 20, root `pnpm install` and filtered build, `packageManager` versus `version`, SHA pins.
- #944 version bump: `workspace:*` versus `workspace:^`.
- #711 Stellar: major/minor locked to protocol and `@x402/core`, patch independent.
- #2579 Keeta: copy `publish_npm_scoped_x402_evm.yml`.
- #1036 changelog template: skip docs-only and ecosystem, keep internal changes.
- #925 docs workflow: also exclude `typescript/site/**` so ecosystem edits do not rebuild docs.
- #3124: one changeset per package when the packages ship separately.
