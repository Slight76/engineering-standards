# Changelog

All notable changes to the engineering standards handbook. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow SemVer.

## 1.1.0 - 2026-10-07

### Added

- `docs/agent-orchestration.md`: running autonomous agent loops with Noodle as the reference implementation. Worktree isolation and the normal merge path, process skills vs task orders, the handbook and evidence contract for workers, supervised-to-automatic oversight with a recorded decision, backlog quality and runtime-state hygiene. Rules ORCH-001..ORCH-005 (Proposed, ADR-0001).
- Catalog (27 rules), skill description, read-by-task map, and catalog digest updated for the ORCH prefix.

## 1.0.1 - 2026-10-07

- Declare `license: MIT` in the skill frontmatter so `gh skill publish` validates cleanly.
- Pin the shared docs-lint workflow to a marketplace commit SHA.

## 1.0.0 - 2026-10-07

First release as a standalone handbook, split out of `architecture-standards` (see [ADR-0001](adr/0001-adopt-engineering-standards.md) and architecture-standards ADR-0028..0031).

### Moved from architecture-standards@c1bda3d

Rule IDs, statements, and historical ADR references are unchanged; wording was adjusted from enterprise/corporate framing to team framing and links were rewritten for the new location.

| Document | Former path | Rules |
| --- | --- | --- |
| `docs/adoption-process.md` | `governance/adoption.md` | — |
| `docs/agent-development-standard.md` | `governance/agent-development-standard.md` | AGT-001..AGT-004 (ADR-0024) |
| `docs/backend-implementation-standard.md` | `backend/implementation-standard.md` | BE-006..BE-009 (ADR-0018) |
| `docs/frontend-implementation-standard.md` | `frontend/implementation-standard.md` | FE-006..FE-009 (ADR-0017) |
| `docs/testing-standard.md` | `platform/testing-standard.md` | TEST-001..TEST-003 (ADR-0020) |

### Added

- `docs/source-control-and-branching.md`: `main`/`develop`, prefixed work branches, Conventional Commits, squash and `--no-ff` merges, SemVer tags, PR size, branch protection. Rules SCM-001..SCM-004 (Proposed, ADR-0001).
- `docs/code-review-checklist.md`: author and reviewer responsibilities, checklist, turnaround targets, agent-authored PR review. Rules REV-001..REV-003 (Proposed, ADR-0001).
- `docs/engineering-fundamentals-checklist.md`: one-page checklist linking every handbook's fundamentals.
- `docs/developer-inner-loop.md`: local setup, devcontainer, standard `dotnet`/`npm` verbs, hooks, running tests locally.
- `docs/dev-lifecycle.md`: stub with intended outline; will replace the copilot-config dev-lifecycle instructions.
- `adr/0001-adopt-engineering-standards.md`: decision to create this handbook and home for SCM/REV rules.
- `skills/engineering-standards/`: agent skill with `references/read-by-task.md` and generated `references/catalog-digest.md`.
- `catalog/catalog.json` (22 rules), `plugin.json`, `AGENTS.md`, `CLAUDE.md`, shared lint and link-check configuration, and the reusable `docs.yml` workflow.
