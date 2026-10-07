---
title: "Source control, branching, and commits"
status: proposed
version: 1.0.0
owner: "@Slight76"
---
# Source control, branching, and commits

Baseline: 1.0.0. Applies when: a repository owned by the team holds application, infrastructure, or standards code

Decision: [ADR-0001](../adr/0001-adopt-engineering-standards.md). Rules become binding when this baseline is adopted; examples explain the policy and do not establish business requirements.

This document describes how the team uses Git and GitHub. It is deliberately small: two long-lived branches, short-lived work branches, one commit format, and a predictable merge strategy so that humans and agents produce the same history.

## Long-lived branches

| Branch | Purpose | Deploys to |
| --- | --- | --- |
| `main` | Released code. Every commit on `main` is a release candidate and carries, or is reachable from, a SemVer tag. | Production (Fly.io production app) |
| `develop` | Integration branch. Completed work lands here first and is deployed automatically for verification. | Staging (Fly.io staging app) |

Small repositories such as the standards handbooks and templates MAY use `main` only; the application repositories use both. Record the choice in the repository README. Do not create additional long-lived branches (`release/*`, `v2`, personal branches); use tags for releases and work branches for everything else.

## Work branches

Branch from `develop` (or `main` in a `main`-only repository) using a type prefix and a short kebab-case description. Include the issue number when one exists.

| Prefix | Use for | Example |
| --- | --- | --- |
| `feature/` | New behaviour or capability | `feature/142-invoice-export` |
| `fix/` | Defect fix that is not urgent | `fix/151-null-tenant-header` |
| `hotfix/` | Urgent production fix branched from `main` | `hotfix/160-token-refresh-loop` |
| `chore/` | Dependencies, tooling, build, CI | `chore/bump-dotnet-sdk` |
| `docs/` | Documentation only | `docs/adr-0007-caching` |

Work branches live for days, not weeks. Rebase or merge `develop` into a work branch before opening a pull request so that the PR shows only its own changes. Delete the branch after merge; repositories enable "delete branch on merge".

A `hotfix/*` branch merges to `main` first, is tagged and deployed, then is merged back into `develop` in the same pull request series so the two branches never diverge for long.

## Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/): `<type>(<optional scope>): <imperative summary>` with types `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `perf`, `build`, `ci`. Keep the summary under 72 characters. Mark a breaking change with `!` after the type and a `BREAKING CHANGE:` footer. Reference issues in the footer (`Closes #142`). When an agent authors a commit, add its co-author trailer so provenance is visible in the history.

The commit type determines the SemVer bump: `feat` → minor, `fix`/`perf` → patch, `!` or `BREAKING CHANGE` → major. Other types do not bump the version by themselves.

## Merging

| Target | Strategy | Reason |
| --- | --- | --- |
| Work branch → `develop` | Squash merge. The squash message is a single Conventional Commit. | One reviewable commit per change; noisy WIP commits disappear. |
| `develop` → `main` | Merge commit (`--no-ff`), never squash. | Preserves the individual squashed commits so release notes and `git bisect` keep working. |
| `hotfix/*` → `main` | Squash merge, then `main` → `develop` merge commit. | Same one-change-one-commit property on `main`. |

Never force-push `main` or `develop`. Force-pushing a work branch is allowed before review starts; after review starts, add commits so reviewers can see what changed, and let the squash tidy up.

## Releases and tags

Tag releases on `main` as `vMAJOR.MINOR.PATCH` (annotated tags, created by the release workflow or the maintainer, not by agents). Pre-releases use `-rc.N`. A tag is immutable: fix forward with a new patch release rather than moving a tag. Consumers of the standards handbooks pin 40-character commit SHAs, so tags are a human convenience and never the only identifier.

Each application repository keeps a `CHANGELOG.md` grouped by release; the entries are generated or hand-written from the Conventional Commit summaries.

## Pull request size

Aim for pull requests under roughly 400 changed lines excluding generated files, lockfiles, and snapshots. Split a larger change into a sequence: contracts/migrations first, implementation second, cleanup last, each independently releasable behind a flag where needed. A PR that cannot be reviewed in one sitting is too big; the reviewer MAY ask for a split without reviewing the content first. Agents open a draft PR early and convert it when the checklist in [code-review-checklist.md](code-review-checklist.md) is satisfied.

## Branch protection

Configure `main` and `develop` as protected branches: pull request required, at least one approving review from someone other than the author, required status checks (build, tests, lint, and the standards validator where it applies), conversations resolved, linear history on `develop` (squash only), force-push and deletion disabled. Administrators do not bypass protection except for a documented incident, and the bypass is recorded in the incident record.

## Repository hygiene

Every repository has a `.gitattributes` with `* text=auto eol=lf`, a `.gitignore` for its stack, an `.editorconfig`, a `LICENSE`, a `README.md` with the documented install/build/test commands required by TEST-003, and an `AGENTS.md` bootstrap when agents work in it. Never commit secrets, local configuration with credentials, build output, or production data; if a secret is committed, rotate it immediately and then rewrite history only if the repository is private and all collaborators agree.

## Rules and required evidence

| ID | Requirement | Verification |
| --- | --- | --- |
| SCM-001 | Work MUST happen on prefixed short-lived branches (`feature/`, `fix/`, `hotfix/`, `chore/`, `docs/`) and never directly on `main` or `develop`. | Branch protection settings and branch-name check in CI |
| SCM-002 | Commits reaching `develop` or `main` MUST use the Conventional Commits format with a type that maps to the SemVer bump. | Commit-message lint on pull requests and squash messages |
| SCM-003 | Work branches MUST squash-merge into `develop`; `develop` MUST merge into `main` with a non-fast-forward merge commit; `main` and `develop` MUST NOT be force-pushed. | Repository merge settings and protected-branch configuration review |
| SCM-004 | Releases on `main` MUST be identified by immutable annotated SemVer tags and a changelog entry. | Tag listing matches CHANGELOG.md; tags never move |

## Exceptions

Use the [exception record](https://github.com/Slight76/standards-marketplace/blob/main/templates/exception.md) for a departure. Record affected rules, scope, compensating controls, approval evidence, expiry, and migration path. Agents must not silently replace defaults.
