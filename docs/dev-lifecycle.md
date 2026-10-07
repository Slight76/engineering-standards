---
title: "Development lifecycle"
status: proposed
version: 1.0.0
owner: "@Slight76"
---
# Development lifecycle

Baseline: 1.0.0. Applies when: a team application moves work from idea to production

Decision: [ADR-0001](../adr/0001-adopt-engineering-standards.md). This document is a stub and defines no rules.

To be authored; will replace the copilot-config dev-lifecycle instructions. Until then, the lifecycle is implied by [source-control-and-branching](source-control-and-branching.md), [code-review-checklist](code-review-checklist.md), [developer-inner-loop](developer-inner-loop.md), and [testing-standard](testing-standard.md).

## Intended sections

- Intake: issue templates, acceptance criteria, sizing, and when a change needs an ADR first
- Planning: breaking work into PR-sized slices; contract and migration ordering
- Build: inner loop, branch and commit conventions, agent participation and handoff records
- Verify: test boundaries, evidence statuses, required CI checks
- Review: human approval, agent-authored PR checks, turnaround targets
- Release: `develop` → `main` merge, SemVer tags, changelog, staged rollout on Fly.io
- Operate: monitoring the release, rollback triggers, incident and postmortem links to the operations handbook
- Learn: retrospectives, standards feedback, proposing rule changes through the marketplace
