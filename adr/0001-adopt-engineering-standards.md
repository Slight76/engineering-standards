# ADR-0001: Adopt the engineering standards handbook

Status: Accepted

Date: 2026-10-07

Owner: @Slight76

## Context

The former single `architecture-standards` repository (v0.3.0) mixed every domain into one catalog and skill. Engineering practice documents (testing, backend and frontend implementation, agent development, adoption process) sat beside solution-architecture and enterprise-governance material, so a developer or agent asking "how do I branch, commit, review, and test here?" had to load the whole corpus. The split into five handbooks (architecture-standards ADR-0028..0031) assigns day-to-day engineering practice to this repository and adds the documents that were previously missing: source control and branching, code review, the engineering fundamentals checklist, the developer inner loop, and a placeholder for the development lifecycle.

## Decision

Create `engineering-standards` as the home for engineering standards, versioned independently from the other handbooks and installable as an agent skill. Documents moved here keep their rule IDs and historical ADR references; new documents are first drafts with `status: proposed`.

New rules introduced by this handbook (SCM-001..SCM-004 for source control, REV-001..REV-003 for code review, and later ORCH-001..ORCH-005 for agent orchestration, added in 1.1.0) are recorded against this ADR with `status: Proposed`. They become binding for an application when its `architecture-baseline.json` pins a revision of this repository that contains them.

## Alternatives

- Keep the domain inside `architecture-standards`: rejected; one repository was too broad to read or install selectively.
- Rewrite all rules from scratch: rejected; existing rules are kept verbatim to preserve consumer baselines.
- Leave branching and review conventions as unversioned wiki or copilot-config instructions: rejected; conventions that CI and reviewers enforce need rule IDs and a changelog.

## Consequences

Consumers pin this repository in `architecture-baseline.json` (`standards[]`). Historic decisions remain in `architecture-standards/adr/` and are declared in `catalog/catalog.json` under `externalDecisions`. New engineering rules are decided locally in this repository's `adr/` directory. The marketplace `catalog/rule-index.json` must be regenerated when rules are added or retired here.

## Traceability

Rule prefixes: AGT, BE, FE, TEST, SCM, REV, ORCH. Related: standards-marketplace ADR-0001; architecture-standards ADR-0028..0031; external decisions ADR-0017, ADR-0018, ADR-0020, ADR-0024.

## Verification

`validate.py` passes; `docs.yml` green on `main`; the skill installs through the standards marketplace.

## Approval

@Slight76, 2026-10-07, plan approved in the split planning session.
