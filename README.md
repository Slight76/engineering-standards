# engineering-standards

Team engineering standards for developers and AI agents: source control and branching, code review, testing, backend and frontend implementation, agent development, agent orchestration, and the standards adoption process.

Part of the Slight76 standards handbooks indexed at [standards-marketplace](https://github.com/Slight76/standards-marketplace). Written for a small team and its AI agents.

## Documents

| Document | Covers | Rule prefixes |
| --- | --- | --- |
| [source-control-and-branching](docs/source-control-and-branching.md) | `main`/`develop`, prefixed work branches, Conventional Commits, squash and `--no-ff` merges, SemVer tags, PR size, branch protection | SCM |
| [code-review-checklist](docs/code-review-checklist.md) | Author and reviewer responsibilities, review checklist, turnaround targets, reviewing agent-authored PRs | REV |
| [testing-standard](docs/testing-standard.md) | Test at the boundary that proves the claim, determinism and isolation, negative cases, commands and evidence | TEST |
| [backend-implementation-standard](docs/backend-implementation-standard.md) | ASP.NET Core project layout, dependency matrix, use-case transactions, lifetimes, async and cancellation, DTO boundaries | BE |
| [frontend-implementation-standard](docs/frontend-implementation-standard.md) | React/TypeScript feature layout, import matrix, state ownership, strict typing, API adapters | FE |
| [agent-development-standard](docs/agent-development-standard.md) | Retrieval order, implementation loop, evidence semantics, drift recovery, handoff records for coding agents | AGT |
| [agent-orchestration](docs/agent-orchestration.md) | Running autonomous agent loops (Noodle as reference): worktree isolation and merge path, process skills vs task orders, handbook and evidence contract, supervised-to-automatic oversight, backlog quality and runtime-state hygiene | ORCH |
| [adoption-process](docs/adoption-process.md) | Normative language, baseline pinning, exceptions and precedence, manifest and evidence files | — |
| [engineering-fundamentals-checklist](docs/engineering-fundamentals-checklist.md) | One-page checklist linking every handbook's fundamentals for a repository or release | — |
| [developer-inner-loop](docs/developer-inner-loop.md) | Local setup, devcontainer, standard `dotnet`/`npm` verbs, pre-commit hooks, running tests locally, fast feedback | — |
| [dev-lifecycle](docs/dev-lifecycle.md) | Stub: idea-to-production lifecycle outline (to be authored) | — |

## Read by task

See [skills/engineering-standards/SKILL.md](skills/engineering-standards/SKILL.md).

## Install as an agent skill

| Agent | Command |
| --- | --- |
| Copilot CLI | `copilot plugin marketplace add Slight76/standards-marketplace` then `copilot plugin install engineering-standards@slight76-standards` |
| GitHub CLI (any agent) | `gh skill install Slight76/engineering-standards engineering-standards --scope user --pin v1.0.0` |
| Claude Code | `/plugin marketplace add Slight76/standards-marketplace` then `/plugin install engineering-standards@slight76-standards` |

## Layout

| Path | Purpose |
| --- | --- |
| `docs/` | Standards documents (frontmatter, applies-when, rule table) |
| `catalog/catalog.json` | Machine-readable rules; `externalDecisions` points at historic ADRs |
| `adr/` | Decisions local to this handbook |
| `skills/engineering-standards/` | Agent skill and references |
| `CHANGELOG.md` | Release history, including documents moved from `architecture-standards` |

Validation: `py ../standards-marketplace/tooling/validate.py --root .`. License: [MIT](LICENSE).
