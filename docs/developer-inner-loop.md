---
title: "Developer inner loop"
status: proposed
version: 1.0.0
owner: "@Slight76"
---
# Developer inner loop

Baseline: 1.0.0. Applies when: a developer or agent builds, runs, or tests a team application locally

Decision: [ADR-0001](../adr/0001-adopt-engineering-standards.md). This page defines no rules of its own; it sets the expectations that make TEST-003 (documented, executable verification commands) and AGT-004 (honest evidence) achievable in practice.

The inner loop is everything between editing a line and knowing whether it works. The target is a clean clone to a passing test run in under fifteen minutes on a fresh machine, and a sub-ten-second edit-to-feedback cycle for unit tests. Fast feedback is a design goal, not a nicety: slow loops push people and agents toward skipping checks.

## Reference stack

| Layer | Tooling |
| --- | --- |
| Backend | .NET SDK pinned in `global.json`; ASP.NET Core; EF Core; xUnit |
| Frontend | Node LTS pinned in `.nvmrc`/`engines`; TypeScript strict; Vite; Vitest + Testing Library; Playwright |
| Database | Postgres, run locally through Docker Compose or Testcontainers; never a shared remote instance |
| Hosting | Fly.io (`fly.toml` committed); local runs do not need Fly credentials |
| Source and CI | GitHub, GitHub Actions |

Pin the toolchain versions in the repository so `dotnet --version` and `node --version` agree across laptops, devcontainers, and CI.

## Local setup expectations

- One documented bootstrap step. `README.md` lists the exact commands; nothing lives only in someone's shell history. Prefer a `justfile`, `Makefile`, or `package.json`/`dotnet` scripts so the same verbs work everywhere.
- A `.devcontainer/devcontainer.json` is provided for every application repository so a developer, GitHub Codespaces, or a cloud agent gets identical SDKs, Node, Postgres, and CLI tools without manual installation.
- Local configuration comes from `appsettings.Development.json`, `.env.local` (git-ignored), or user secrets (`dotnet user-secrets`). Templates (`.env.example`) are committed; real values never are.
- Local services start with `docker compose up -d` from a committed `compose.yaml`. The compose file uses the same Postgres major version as production.
- Database schema is created by running the EF Core migrations against the local database, never by restoring a production dump.

## Standard verbs

Every application exposes the same verbs regardless of stack so humans and agents can guess correctly:

| Verb | Backend | Frontend |
| --- | --- | --- |
| restore | `dotnet restore` | `npm ci` |
| build | `dotnet build --no-restore -warnaserror` | `npm run build` |
| test | `dotnet test --no-build` | `npm test` (Vitest) |
| test:integration | `dotnet test --filter Category=Integration` | `npm run test:e2e` (Playwright) |
| lint | `dotnet format --verify-no-changes` | `npm run lint` and `npm run typecheck` |
| run | `dotnet run --project src/Api` or `dotnet watch` | `npm run dev` |

Unit tests MUST run without network access or a database. Integration tests declare their dependency on Postgres (or another service) and are skipped with an explicit `not_run` message, not a silent pass, when the dependency is unavailable.

## Pre-commit and pre-push checks

Install the repository's git hooks (`husky` for Node repositories, `dotnet tool` based hooks or `lefthook` for .NET) on bootstrap. The hooks run in seconds and cover:

- Format and lint on staged files only (`dotnet format`, ESLint, Prettier, markdownlint).
- Commit message validation against Conventional Commits.
- Secret scanning (for example gitleaks) on staged content.
- Type check (`tsc --noEmit`) on pre-push for frontend repositories.

Hooks are a convenience for fast feedback; CI runs the same checks authoritatively. A hook MAY be bypassed with `--no-verify` to save work-in-progress on a work branch, never to merge.

## Running tests locally

1. `restore`, `build`, `test` from a clean clone before claiming anything passes. Agents run these from the documented clean state (AGT-004).
2. Use the watch modes (`dotnet watch test`, `vitest --watch`) while iterating; run the full `test` and `lint` verbs before opening a pull request.
3. Run `test:integration` whenever a change touches persistence, HTTP boundaries, auth, or CORS. These tests use a disposable Postgres (Testcontainers or compose) and synthetic data (TEST-002).
4. Run the Playwright journey suite before merging a user-visible frontend change; use the real API host where the change crosses the boundary.
5. Capture the exact command and result in the PR description. If a suite could not run locally (for example no Docker), say so and let CI provide the evidence.

## Fast feedback practices

- Keep unit suites under a minute and integration suites under ten minutes; split or parallelize when they grow past that.
- Fail fast: enable `-warnaserror`, `TreatWarningsAsErrors`, strict TypeScript, and `eslint --max-warnings 0` so warnings never accumulate.
- Hot reload (`dotnet watch`, Vite HMR) for UI and API changes; do not restart containers to see a code change.
- Seed scripts create a realistic synthetic dataset for manual testing in seconds; they never pull production data.
- Keep the devcontainer and CI images in sync; if CI needs a tool, the devcontainer gets it in the same PR.

## Agent considerations

Agents working in a repository follow the same loop. They read `README.md` and `AGENTS.md`, use the standard verbs rather than inventing commands, run checks from a clean state, and report each result with the evidence status defined in [agent-development-standard](agent-development-standard.md). An agent that cannot start Postgres or a browser reports `not_run` for the affected suites instead of substituting mocks and calling the integration tests passed.

## Related

[testing-standard](testing-standard.md) for what to test and where; [source-control-and-branching](source-control-and-branching.md) for branch and commit conventions; [code-review-checklist](code-review-checklist.md) for what reviewers expect in the PR description.
