---
title: "Agent orchestration"
status: proposed
version: 1.0.0
owner: "@Slight76"
---
# Agent orchestration

Baseline: 1.0.0. Applies when: a team repository runs an autonomous or semi-autonomous loop that schedules, dispatches, and merges work performed by coding agents

Decision: [ADR-0001](../adr/0001-adopt-engineering-standards.md). Rules become binding when this baseline is adopted; examples explain the policy and do not establish business requirements.

An orchestration loop turns a backlog into pull requests without a human typing each prompt: a scheduler reads project state and writes orders, workers execute each order in isolation, and completed work merges back through review. This document sets the rules for running such a loop so that it produces the same history, evidence, and review trail as a human developer following [source-control-and-branching](source-control-and-branching.md) and [code-review-checklist](code-review-checklist.md). It does not repeat how a single agent reasons about a task; that is [agent-development-standard](agent-development-standard.md) (AGT-001..AGT-004), which every worker in a loop also follows.

The rules are tool-agnostic. [Noodle](https://github.com/poteto/noodle) is the team's reference implementation and is used for the examples below; any loop with the same shape (schedule, dispatch, execute, merge) is in scope.

## Vocabulary

| Term | Meaning |
| --- | --- |
| Loop | The long-running process that gathers state, schedules, dispatches workers, and merges results. |
| Backlog item | A unit of work the loop may pick up, held in the repository (`todos.md`) or synced from an issue tracker by an adapter. |
| Order | A scheduled instance of work: one backlog item (or a standalone infrastructure task) with a pipeline of stages such as execute, quality, reflect. |
| Skill | A `SKILL.md` directory loaded into a worker as its instructions. Scheduled skills carry a `schedule` field and become task types the scheduler can place in an order. |
| Adapter | A script that bridges the backlog to an external system (`sync`, `add`, `done`, `edit`). |
| Mode | The human-oversight level of the loop: supervised (a human approves merges), manual (a human triggers each step), or automatic. |

## Isolation and merge path

Every order executes in its own git worktree on a short-lived work branch created from the integration branch (`develop`, or `main` in a `main`-only repository). Workers never edit the primary checkout; concurrent sessions on one checkout lose work and corrupt each other's commits. Branch names follow SCM-001 (`feature/`, `fix/`, `chore/`, `docs/` plus the backlog item ID) so loop-authored branches are indistinguishable from human ones in protection rules and CI.

Completed work reaches `develop` or `main` only through the repository's normal review path: a pull request, the required status checks, and the review required by REV-001. A loop MAY open the pull request, push follow-up commits, and merge when the configured mode and branch protection allow it; it MUST NOT push to a protected branch, bypass a required check, or merge its own work while the mode is supervised. Squash and `--no-ff` behaviour follows SCM-003 regardless of who presses merge. Stale worktrees from crashed sessions are listed and pruned at loop start, never silently merged.

## Skills describe process, orders describe tasks

A skill is reusable across every backlog item. It says how work is done in this repository: worktree workflow, the verification commands for the stack, commit conventions, scope discipline, what to read before starting. The order (its `prompt` and `extra_prompt`) says what to do for this item. Mixing the two means the next item inherits stale task detail and the skill cannot be reviewed independently of any one task.

Skills therefore contain no current task names, no issue numbers, no feature-specific branches ("for auth work, do X"), and no values that differ between environments or people: no tokens, hostnames, local paths, provider API keys, or model identifiers that belong in configuration. Examples in a skill use generic placeholders. Where a skill must reference a principle or standard, it names the file to read at run time rather than inlining the text, so the worker picks up changes without a skill edit.

## Orders carry the handbook and the verification contract

The scheduler is the only component that writes orders. Each dispatched order names, directly or through the skill it loads, the handbook documents the worker must read for the task (the pinned revision comes from the consumer's `architecture-baseline.json`, never from latest `main`) and the verification commands the worker must run before it commits. A worker that cannot resolve the pinned baseline reports the blocker rather than substituting a different revision (AGT-001).

Worker output is a pull request whose description satisfies REV-002: baseline revision, rule IDs applied, commands actually run with results, remaining risks. The worker updates the consumer's `implementation-evidence.json` with `passed`, `failed`, `not_run`, `not_applicable`, or `excepted` entries as the consumer's `AGENTS.md` requires (AGT-002). A quality or review stage that follows execute checks these claims the same way a human reviewer does under REV-003; it does not patch a false `passed`, it fails the order.

## Oversight modes and the path to automatic merging

A new loop starts in supervised mode: it schedules, executes, opens pull requests, and waits for a human to approve and merge. This is the default and needs no decision record. Moving to automatic merging is a change in who holds the merge decision and is recorded before it happens, as an ADR in the consuming repository or an adoption record under [adoption-process](adoption-process.md). The record states:

- the scope (which repository, which branches, which task types may auto-merge);
- the rollback: how to stop the loop, revert an auto-merged change, and return to supervised mode;
- the concurrency cap: the maximum number of worker sessions at once;
- the cost or budget limit: tokens, sessions per day, or spend, and who is told when it is reached;
- the review that still applies, since REV-001 requires a human approval on protected branches unless an accepted exception says otherwise.

Automatic merging without that record is a departure from REV-001 and is handled as an exception, not as a configuration change.

## Backlog quality and repository hygiene

A loop executes what the backlog says, so the backlog is the specification. Each item the loop may pick up has an owner, acceptance criteria a worker can check, and a done signal the loop can verify (a passing command, a closed issue through the adapter, a checked item). An item missing any of these is refined first, by a human or by a plan-first order that produces a plan for review, and is not dispatched to execute.

The loop's runtime state, including brief, live orders, session logs, and worktree bookkeeping (`.noodle/` in the reference implementation), is machine-generated and gitignored. The loop's configuration (`.noodle.toml`), adapter scripts, and skills are source: committed, reviewed through pull requests like any other code, and changed on a work branch. A skill or adapter change that alters what workers do is reviewed under [code-review-checklist](code-review-checklist.md) with attention to scope, secrets, and the commands it will run autonomously.

## Reference implementation: Noodle

| Concern | Noodle |
| --- | --- |
| Install | Binary on `PATH` (`noodle --version`), then the `noodle` skill copied verbatim to `.agents/skills/noodle/` per [INSTALL.md](https://github.com/poteto/noodle/blob/main/INSTALL.md). |
| Configuration | `.noodle.toml` at the project root: `mode`, `[routing.defaults]` provider and model, `[skills] paths`, `[concurrency] max_concurrency`, `[adapters.backlog.scripts]`. Committed. |
| Runtime state | `.noodle/` (`mise.json`, `orders-next.json`, `orders.json`, session logs). Gitignored. |
| Scheduler | `schedule` skill reads `.noodle/mise.json` and writes `.noodle/orders-next.json`; the loop promotes it atomically. One plan at a time; empty orders when nothing is actionable. |
| Worker | `execute` skill: scope, decompose, implement in a worktree (`noodle worktree create`, `noodle worktree exec`, `noodle worktree merge`), verify, commit. Followed by `quality` and optionally `reflect` stages. |
| Backlog | `todos.md` by default, or an adapter (`sync`, `add`, `done`, `edit` scripts) to an issue tracker. |
| Oversight | `mode = "supervised"` (human approves merges), `manual`, or `auto`. Start supervised. |
| Run | `noodle start` (loop), `noodle start --once` (one cycle), `noodle status`, `noodle worktree list` / `prune`. |

The team template for a consumer repository, including a `.noodle.toml`, schedule and execute skills that cite this handbook, and a `.gitignore` entry, is in the marketplace [consumer-kit/noodle](https://github.com/Slight76/standards-marketplace/blob/main/consumer-kit/noodle/README.md).

## Rules and required evidence

| ID | Requirement | Verification |
| --- | --- | --- |
| ORCH-001 | Autonomous agent loops MUST run every unit of work in an isolated git worktree on a short-lived work branch, never on `main` or `develop`, and MUST merge back only through the repository's normal review path (SCM-001, SCM-003, REV-001). | Worktree and branch listing during a run; branch protection settings; merged-PR audit shows a PR and review for each loop-authored change |
| ORCH-002 | Orchestration skills MUST describe process, not tasks: task context lives in the order or backlog item, skills are reusable across every item, and skills contain no secrets or environment-specific values. | Skill review on each change; grep for task IDs, hostnames, tokens, and local paths in `SKILL.md` files |
| ORCH-003 | Every order dispatched to a worker MUST name the handbook documents to read at the pinned baseline revision and the verification commands to run; worker output MUST satisfy REV-002 and update `implementation-evidence.json` as the consumer's `AGENTS.md` requires. | Order and skill inspection; PR description check; evidence file diff in the worker's PR |
| ORCH-004 | Loops MUST start in a supervised mode where a human approves merges and MAY move to automatic merging only after a recorded decision that defines rollback, a concurrency cap, and a cost or budget limit. | Loop configuration shows the mode; ADR or adoption record exists before `auto` is enabled |
| ORCH-005 | Backlog items consumed by a loop MUST have an owner, acceptance criteria, and a loop-checkable done signal, and items lacking them MUST be refined rather than executed; loop runtime state MUST be gitignored while configuration, adapters, and skills MUST be committed and reviewed like code. | Backlog sample review; scheduler refuses or plan-firsts unrefined items; `.gitignore` contains the runtime directory; config, adapters, and skills tracked in git |

## Exceptions

Use the [exception record](https://github.com/Slight76/standards-marketplace/blob/main/templates/exception.md) for a departure. Record affected rules, scope, compensating controls, approval evidence, expiry, and migration path. Agents must not silently replace defaults.
