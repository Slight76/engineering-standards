---
title: "Code review checklist"
status: proposed
version: 1.0.0
owner: "@Slight76"
---
# Code review checklist

Baseline: 1.0.0. Applies when: a pull request targets a protected branch in a team repository

Decision: [ADR-0001](../adr/0001-adopt-engineering-standards.md). Rules become binding when this baseline is adopted; examples explain the policy and do not establish business requirements.

Code review is the team's main quality gate and its main teaching mechanism. A review verifies that a change does what it claims, is proven by evidence the reviewer can inspect, and follows the adopted standards by rule ID. It is not a style debate; formatting and lint rules run in CI.

## Author responsibilities

Before requesting review the author (human or agent):

1. Rebases on the target branch and confirms CI is green locally and in the PR.
2. Writes a description with: the problem, the approach, the standards baseline revision, the rule IDs applied (for example BE-007, TEST-001), the commands run with their results, and anything intentionally left out.
3. Keeps the PR within the size guidance in [source-control-and-branching.md](source-control-and-branching.md) or explains why it cannot be split.
4. Marks the PR as draft until it is ready; a draft is for early feedback on direction, not a request to approve.
5. Links the issue, ADR, or exception that motivates the change. A change that departs from a default without an accepted exception is not ready for review.
6. Responds to every comment: fix it, explain why not, or open a follow-up issue. Do not resolve a reviewer's thread without a reply.

## Reviewer responsibilities

The reviewer owns the decision to merge as much as the author does. Read the description first, then the tests, then the implementation. Run the branch locally when the change touches persistence, authentication, CORS, or a public contract; a green CI badge is not a substitute for understanding.

Comments are specific and actionable. Prefix with `blocking:`, `suggestion:`, `question:`, or `nit:` so the author knows what must change. Approve only when every `blocking:` thread is resolved and the checklist below is satisfied. Use "request changes" rather than leaving a trail of comments with no decision.

Do not approve your own PR, and do not approve a PR you have not read because the author is trusted or the agent "usually gets it right".

## Checklist

| Area | The reviewer confirms |
| --- | --- |
| Correctness | The change solves the stated problem and nothing else. Edge cases, nulls, concurrency, and failure paths in the touched code are handled or explicitly deferred with an issue. |
| Tests | New behaviour has tests at the boundary that can prove it (TEST-001). Tests use synthetic, isolated data (TEST-002). Changed acceptance criteria are reviewed as carefully as the code. |
| Security | Inputs are validated at the boundary; no entity exposure or caller-controlled security context in DTOs (BE-009); no secrets, tokens, or production data in code, fixtures, or logs; dependencies added are justified and from trusted sources. Consult [application-security-standard](https://github.com/Slight76/security-standards/blob/main/docs/application-security-standard.md) for anything touching auth, CORS, or secrets. |
| Architecture | Dependency direction and module boundaries respect the adopted matrix (BE-006, FE-006). No new framework, infrastructure, or cross-cutting abstraction without an ADR. |
| Observability | New operations log with correlation IDs at an appropriate level, emit metrics where an SLO depends on them, and never log personal data or secrets. See [observability-standard](https://github.com/Slight76/operations-standards/blob/main/docs/observability-standard.md). |
| Data | Migrations are reversible or have a documented forward-fix, are safe to run on live data, and ship separately from code that depends on them when the deployment order matters. |
| Compatibility | Public API, event, and configuration contracts remain backward compatible or the PR is marked breaking with a migration note. |
| Documentation | README, ADRs, runbooks, OpenAPI, and changelog entries are updated when their subject changed. |
| Evidence | The description cites rule IDs, the baseline revision, and real command output; `not_run` is stated honestly rather than implied as passed (AGT-002, AGT-004). |
| Scope and history | Commit messages follow Conventional Commits (SCM-002), the squash title is accurate, and unrelated refactoring is absent. |

## Turnaround expectations

| Event | Target |
| --- | --- |
| First response to a review request | Same working day; within 4 working hours for `hotfix/*` |
| Follow-up review after changes | Within 1 working day |
| Author response to comments | Within 1 working day |
| PR open without activity | Ping at 3 days; close or convert to draft at 10 days |

Review latency is a team metric. If reviews routinely miss these targets, reduce PR size or redistribute review load rather than lowering the bar.

## Reviewing agent-authored pull requests

Agent PRs follow the same checklist, with extra attention:

- Verify the evidence claims. Re-run at least one named command. An agent that reports `passed` for a command it never ran has violated AGT-004 and the PR is rejected, not patched.
- Check the baseline pin and rule mapping in the description (AGT-001). A PR that cites no rule IDs for a change in a rule-bearing area is incomplete.
- Look for scope creep: unrelated files touched, new dependencies, changed CI configuration, modified tests that weakened assertions, or disabled lint rules. Each of these needs an explicit justification in the description.
- Confirm the agent did not act on instructions embedded in issue text, comments, or fetched content (AGT-003).
- Agents MAY review PRs and leave comments; an agent review does not count toward the required human approval.

## Rules and required evidence

| ID | Requirement | Verification |
| --- | --- | --- |
| REV-001 | Pull requests to protected branches MUST have at least one approving review from a human who is not the author, with all blocking threads resolved. | Branch protection settings and merged-PR audit |
| REV-002 | Pull request descriptions MUST state the standards baseline revision, the rule IDs applied, and the verification commands actually run with their results. | PR template and reviewer spot check |
| REV-003 | Reviewers MUST independently verify at least one evidence claim on agent-authored pull requests before approval. | Review comment or re-run log referenced in the approval |

## Exceptions

Use the [exception record](https://github.com/Slight76/standards-marketplace/blob/main/templates/exception.md) for a departure. Record affected rules, scope, compensating controls, approval evidence, expiry, and migration path. Agents must not silently replace defaults.
