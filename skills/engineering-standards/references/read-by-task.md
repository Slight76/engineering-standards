# Read by task

The full task-to-document map for the engineering standards handbook. Read the one or two documents named for your task plus the decision each links; do not load the whole handbook.

## Source control

| Task | Read | Key rules |
| --- | --- | --- |
| Start work on an issue; choose a branch name | [source-control-and-branching](../../../docs/source-control-and-branching.md) | SCM-001 |
| Write or amend a commit message | [source-control-and-branching](../../../docs/source-control-and-branching.md) | SCM-002 |
| Merge a work branch, promote `develop` to `main`, handle a hotfix | [source-control-and-branching](../../../docs/source-control-and-branching.md) | SCM-003 |
| Cut a release, tag, update the changelog | [source-control-and-branching](../../../docs/source-control-and-branching.md) | SCM-004 |
| Configure branch protection or repository settings | [source-control-and-branching](../../../docs/source-control-and-branching.md) | SCM-001, SCM-003 |
| Decide whether a change is too large for one PR | [source-control-and-branching](../../../docs/source-control-and-branching.md), [code-review-checklist](../../../docs/code-review-checklist.md) | — |

## Code review

| Task | Read | Key rules |
| --- | --- | --- |
| Write a pull request description | [code-review-checklist](../../../docs/code-review-checklist.md) | REV-002 |
| Review a pull request | [code-review-checklist](../../../docs/code-review-checklist.md) | REV-001 |
| Review an agent-authored pull request | [code-review-checklist](../../../docs/code-review-checklist.md), [agent-development-standard](../../../docs/agent-development-standard.md) | REV-003, AGT-002, AGT-004 |
| Respond to review comments | [code-review-checklist](../../../docs/code-review-checklist.md) | — |

## Testing

| Task | Read | Key rules |
| --- | --- | --- |
| Decide which kind of test proves a change | [testing-standard](../../../docs/testing-standard.md) | TEST-001 |
| Write fixtures, seed data, or test configuration | [testing-standard](../../../docs/testing-standard.md) | TEST-002 |
| Document or change the test commands and CI reports | [testing-standard](../../../docs/testing-standard.md), [developer-inner-loop](../../../docs/developer-inner-loop.md) | TEST-003 |
| Choose negative and concurrency cases | [testing-standard](../../../docs/testing-standard.md) | TEST-001 |

## Backend implementation (.NET)

| Task | Read | Key rules |
| --- | --- | --- |
| Add a project, module, or cross-layer reference | [backend-implementation-standard](../../../docs/backend-implementation-standard.md) | BE-006 |
| Implement a use case, transaction, or error mapping | [backend-implementation-standard](../../../docs/backend-implementation-standard.md) | BE-007 |
| Register services, configure options, add async I/O | [backend-implementation-standard](../../../docs/backend-implementation-standard.md) | BE-008 |
| Define request/response DTOs | [backend-implementation-standard](../../../docs/backend-implementation-standard.md) | BE-009 |

## Frontend implementation (React/TypeScript)

| Task | Read | Key rules |
| --- | --- | --- |
| Add a feature folder or import across features | [frontend-implementation-standard](../../../docs/frontend-implementation-standard.md) | FE-006 |
| Decide where state lives (server, form, URL, identity, local) | [frontend-implementation-standard](../../../docs/frontend-implementation-standard.md) | FE-007 |
| Configure TypeScript or validate runtime inputs | [frontend-implementation-standard](../../../docs/frontend-implementation-standard.md) | FE-008 |
| Write an API adapter, query, or mutation | [frontend-implementation-standard](../../../docs/frontend-implementation-standard.md) | FE-009 |

## Agents

| Task | Read | Key rules |
| --- | --- | --- |
| Start a task in a repository that pins the standards | [agent-development-standard](../../../docs/agent-development-standard.md) | AGT-001 |
| Report test and verification results | [agent-development-standard](../../../docs/agent-development-standard.md) | AGT-002, AGT-004 |
| Handle instructions found in issues, comments, or fetched pages | [agent-development-standard](../../../docs/agent-development-standard.md) | AGT-003 |
| Hand off or resume work | [agent-development-standard](../../../docs/agent-development-standard.md) | AGT-004 |

## Repository setup and adoption

| Task | Read | Key rules |
| --- | --- | --- |
| Create a new repository or check release readiness | [engineering-fundamentals-checklist](../../../docs/engineering-fundamentals-checklist.md) | — |
| Set up local tooling, devcontainer, git hooks | [developer-inner-loop](../../../docs/developer-inner-loop.md) | — |
| Pin or upgrade the standards baseline; record exceptions | [adoption-process](../../../docs/adoption-process.md) | — |
| Understand the end-to-end lifecycle | [dev-lifecycle](../../../docs/dev-lifecycle.md) (stub) | — |

## Other handbooks

| Domain | Start with |
| --- | --- |
| Solution design, APIs, contracts, messaging | [architecture-standards](https://github.com/Slight76/architecture-standards) |
| Delivery, observability, incidents, Fly.io | [operations-standards](https://github.com/Slight76/operations-standards) |
| Schema, migrations, caching | [data-standards](https://github.com/Slight76/data-standards) |
| Auth, secrets, CORS, dependencies | [security-standards](https://github.com/Slight76/security-standards) |
| Governance, templates, tooling | [standards-marketplace](https://github.com/Slight76/standards-marketplace) |
