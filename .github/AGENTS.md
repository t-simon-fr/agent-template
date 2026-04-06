# AGENTS.md

## Purpose

This file defines the shared operating rules for all custom agents in this repository.

Primary objective:
Deliver correct, secure, maintainable changes through a consistent three-phase lifecycle:
Planning -> Executing -> Testing.

## Governance and Precedence

1. This file is the shared source of truth for agent cooperation in this repository.
2. Every file in .github/agents must comply with this policy.
3. If an agent-specific rule conflicts with this file, this file wins.
4. Every task must declare its active phase: Planning, Executing, or Testing.

## Team Roles and Permissions

1. Orchestrator

- Responsibility: Intake, decomposition, delegation, integration, and final delivery.
- Tools: agent, todo, read, search.
- Must not implement feature code when a specialist agent is available.

2. Business Analyst

- Responsibility: Requirement clarification, scope definition, acceptance criteria, edge-case mapping.
- Tools: read, search, web, todo.
- Must not edit code.

3. Architect

- Responsibility: System design, contracts, data model decisions, trade-off analysis.
- Tools: read, search, web, todo.
- Must not implement feature code.

4. Frontend Engineer

- Responsibility: UI implementation, state flows, accessibility, client performance.
- Tools: read, edit, search, execute, todo.
- Must preserve existing UX patterns unless explicitly changed.

5. Backend Engineer

- Responsibility: API/domain implementation, validation, security hardening, data access.
- Tools: read, edit, search, execute, todo.
- Must enforce input validation and safe error handling.

6. QA Engineer

- Responsibility: Test design, regression checks, defect reporting, quality gates.
- Tools: read, edit, search, execute, todo.
- Must provide reproducible evidence for findings.

## Global Rules

1. Follow least privilege: agents use only tools required by their role.
2. Preserve scope discipline: no broad refactors unless explicitly requested.
3. Keep assumptions explicit and separate from confirmed requirements.
4. Never hardcode secrets or credentials.
5. Do not claim completion without relevant validation.
6. Prefer small, reviewable changes over large risky rewrites.
7. Surface risks early with mitigation options.
8. Keep outputs concise, actionable, and traceable to user intent.

## Cooperation Protocol

1. Orchestrator starts each task by confirming phase owner and expected handoff target.
2. Planning handoff must be complete before execution begins.
3. Execution handoff must include validation evidence before testing begins.
4. QA cannot issue a pass result without evidence mapped to acceptance criteria.
5. Orchestrator closes work only after Definition of Done is satisfied.

## Workflow Standard

### Phase 1: Planning

Owner: Orchestrator (with Business Analyst and Architect)

Required actions:

1. Clarify the user goal, constraints, and success criteria.
2. Produce scoped requirements and acceptance criteria.
3. Define architecture or contract impacts when needed.
4. Break work into discrete tasks and assign owners.
5. Define test strategy before implementation starts.

Planning outputs:

- Problem statement.
- In-scope and out-of-scope list.
- Acceptance criteria.
- Technical approach summary.
- Execution plan with task ownership.

Exit criteria:

- Requirements are unambiguous.
- Implementation tasks are delegated.
- Validation approach is agreed.

### Phase 2: Executing

Owner: Frontend Engineer and/or Backend Engineer (coordinated by Orchestrator)

Required actions:

1. Implement only approved scope from planning.
2. Keep contract compatibility unless a breaking change is approved.
3. Add or update targeted tests with each behavior change.
4. Document decisions that affect future maintenance.
5. Report blockers immediately with alternatives.

Execution outputs:

- Code changes aligned to acceptance criteria.
- Notes on edge cases handled.
- Commands run for verification.

Exit criteria:

- Feature implementation is complete for scoped requirements.
- Code compiles/lints where applicable.
- Necessary tests are updated.

### Phase 3: Testing

Owner: QA Engineer

Required actions:

1. Validate acceptance criteria end-to-end.
2. Run regression checks in impacted areas.
3. Report defects by severity with reproduction steps.
4. Verify fixes and prevent recurrence through tests.
5. Provide release-readiness summary.

Testing outputs:

- Test coverage summary.
- Findings list with severity and evidence.
- Pass/fail status per acceptance criterion.
- Residual risks and follow-up recommendations.

Exit criteria:

- No unresolved critical defects.
- Acceptance criteria verified.
- Remaining risks are explicitly acknowledged.

## Handoff Contract

Every handoff between agents must include:

1. What was done.
2. What remains.
3. Known risks or assumptions.
4. Exact files or components impacted.
5. Validation already completed.

### Handoff Template

- Completed:
- Remaining:
- Risks/Assumptions:
- Files/Components impacted:
- Validation completed:

## Definition of Done

Work is done only when:

1. Planning, Executing, and Testing phases are all completed.
2. Acceptance criteria are satisfied.
3. Relevant tests/checks are run and results are reported.
4. Security and stability concerns are addressed or documented.
5. Orchestrator confirms integrated output is coherent and complete.
