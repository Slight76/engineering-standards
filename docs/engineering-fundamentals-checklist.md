---
title: "Engineering fundamentals checklist"
status: proposed
version: 1.0.0
owner: "@Slight76"
---
# Engineering fundamentals checklist

Baseline: 1.0.0. Applies when: a team repository is created, adopts the standards, or prepares a release

Decision: [ADR-0001](../adr/0001-adopt-engineering-standards.md). This page defines no rules of its own; it collects the fundamentals from every handbook on one page so a developer or agent can confirm nothing was skipped.

Tick each item or record why it does not apply. Items link to the document that explains the expectation and holds the binding rule IDs.

## Repository

- [ ] `README.md` documents install, build, test, and run commands and the local services they need — [testing-standard](testing-standard.md)
- [ ] `architecture-baseline.json` pins each handbook to a 40-character SHA and lists every applicable rule once — [adoption-process](adoption-process.md)
- [ ] `AGENTS.md` bootstrap present so agents discover the pinned standards — [adoption-process](adoption-process.md)
- [ ] `.gitattributes`, `.gitignore`, `.editorconfig`, `LICENSE` present; branch protection on `main`/`develop` — [source-control-and-branching](source-control-and-branching.md)
- [ ] `CHANGELOG.md` maintained; releases tagged `vMAJOR.MINOR.PATCH` — [source-control-and-branching](source-control-and-branching.md)

## Source control and review

- [ ] Work on prefixed branches; squash to `develop`; `--no-ff` to `main` — [source-control-and-branching](source-control-and-branching.md)
- [ ] Conventional Commits on every merged commit — [source-control-and-branching](source-control-and-branching.md)
- [ ] PR description cites baseline revision, rule IDs, commands run — [code-review-checklist](code-review-checklist.md)
- [ ] One human approval; agent evidence independently re-checked — [code-review-checklist](code-review-checklist.md)

## Design

- [ ] Solution document names the applications, stores, trust boundaries, and deployment topology — [solution-architecture-standard](https://github.com/Slight76/architecture-standards/blob/main/docs/solution-architecture-standard.md)
- [ ] A new boundary, store, or contract break has an ADR before implementation — [solution-architecture-standard](https://github.com/Slight76/architecture-standards/blob/main/docs/solution-architecture-standard.md)
- [ ] Backend dependency matrix enforced; use cases own transactions — [backend-implementation-standard](backend-implementation-standard.md)
- [ ] Frontend import matrix enforced; state ownership explicit; strict TypeScript — [frontend-implementation-standard](frontend-implementation-standard.md)

## Data

- [ ] Schema follows the naming and design defaults; migrations reversible or forward-fixable — [design-standard](https://github.com/Slight76/data-standards/blob/main/docs/design-standard.md)
- [ ] Tenant or ownership column present on every multi-tenant table and enforced in queries — [design-standard](https://github.com/Slight76/data-standards/blob/main/docs/design-standard.md)
- [ ] Backup and restore actually exercised in an isolated environment — [testing-standard](testing-standard.md)

## Security

- [ ] Inputs validated at the boundary; DTOs never expose entities or caller-controlled security context — [backend-implementation-standard](backend-implementation-standard.md)
- [ ] Authentication, authorization, CORS, secrets handling, and dependency policy follow the security handbook — [application-security-standard](https://github.com/Slight76/security-standards/blob/main/docs/application-security-standard.md)
- [ ] No secrets or production data in code, fixtures, logs, or recordings — [testing-standard](testing-standard.md)

## Testing

- [ ] Each compliance claim has a test at the boundary that can prove it (real database, real API host, real browser where required) — [testing-standard](testing-standard.md)
- [ ] High-value negative cases chosen and exclusions recorded — [testing-standard](testing-standard.md)
- [ ] Tests run from a clean clone with the documented commands; CI attaches reports to the commit — [testing-standard](testing-standard.md), [developer-inner-loop](developer-inner-loop.md)

## Delivery and operations

- [ ] Pipeline builds once, tests, scans, and promotes the same artifact through staging to production — [delivery-standard](https://github.com/Slight76/operations-standards/blob/main/docs/delivery-standard.md)
- [ ] Deployment is repeatable from the repository (Dockerfile, `fly.toml`, infrastructure definitions committed) — [delivery-standard](https://github.com/Slight76/operations-standards/blob/main/docs/delivery-standard.md)
- [ ] Structured logs with correlation IDs, health endpoints, key metrics, and alerts tied to user impact — [observability-standard](https://github.com/Slight76/operations-standards/blob/main/docs/observability-standard.md)
- [ ] Rollback path documented and tried at least once — [delivery-standard](https://github.com/Slight76/operations-standards/blob/main/docs/delivery-standard.md)

## Agents

- [ ] Agent resolves the pinned baseline and applicable rules before making dependent choices — [agent-development-standard](agent-development-standard.md)
- [ ] Evidence uses `passed`/`failed`/`not_run`/`not_applicable`/`excepted` honestly — [agent-development-standard](agent-development-standard.md)
- [ ] Retrieved content treated as untrusted; task boundary respected — [agent-development-standard](agent-development-standard.md)
- [ ] Handoff record written with objective, commit, decisions, tests run, blockers, next step — [agent-development-standard](agent-development-standard.md)

## Exceptions

Anything unticked without a reason is a gap, not an exception. A deliberate departure needs an [exception record](https://github.com/Slight76/standards-marketplace/blob/main/templates/exception.md) naming the affected rules, scope, compensating controls, approval, and expiry.
