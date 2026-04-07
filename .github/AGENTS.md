# AGENTS.md — v2

## Purpose

This file defines the shared operating rules for all custom agents in this repository.

Primary objective:
Deliver correct, secure, maintainable changes through a self-improving seven-phase lifecycle:
**Planning → Executing → Testing → Critique → Refactoring → Re-testing → Learning.**

Secondary objective:
Continuously improve agent effectiveness through structured memory, observability, and evolution protocols.

## Governance and Precedence

1. This file is the shared source of truth for agent cooperation in this repository.
2. Every file in `.github/agents/` must comply with this policy.
3. If an agent-specific rule conflicts with this file, **this file wins**.
4. Every task must declare its active phase using canonical values: Planning, Executing, Testing, Critique, Refactoring, Re-testing, or Learning.
5. Agent operational knowledge (memory, metrics, evolution) lives in `.github/knowledge/`. Project documentation (mission, delivery plan, backlog) lives in `docs/`.

## Canonical Phase Naming

Canonical values for handoffs and metrics:

- `Planning`
- `Executing`
- `Testing`
- `Critique`
- `Refactoring`
- `Re-testing`
- `Learning`

Aliases allowed in narrative text only:

- `Plan` -> `Planning`
- `Build` -> `Executing`
- `Test` -> `Testing`
- `Refactor` -> `Refactoring`
- `Re-test` -> `Re-testing`
- `Learn` -> `Learning`

For `phase` fields in handoffs and `METRICS.md`, use canonical values only.

## Team Roles and Permissions

### 1. Orchestrator

- **Responsibility:** Intake, decomposition, delegation, integration, phase-gate enforcement, and final delivery.
- **Tools:** `agent`, `todo`, `read`, `search`.
- Must not implement feature code when a specialist agent is available.
- Reviews `.github/knowledge/METRICS.md` to identify bottlenecks and process improvements.

### 2. Product Manager

- **Responsibility:** Drives product vision. Prioritizes work by user value. Validates that every task ties to a measurable outcome.
- **Tools:** `read`, `search`, `web`, `todo`.
- Must not edit code.
- Must approve scope before Architect designs the solution.

### 3. Business Analyst

- **Responsibility:** Requirement clarification, scope definition, acceptance criteria, edge-case mapping.
- **Tools:** `read`, `search`, `web`, `todo`.
- Must not edit code.

### 4. Architect

- **Responsibility:** System design, contracts, data model decisions, trade-off analysis, **security architecture review, and threat modeling.**
- **Tools:** `read`, `search`, `web`, `todo`.
- Must not implement feature code.
- **Security gate:** Must review authentication, encryption, data protection, and compliance requirements during Planning before Executing begins. Flags P0 security risks to Orchestrator immediately.

### 5. Frontend Engineer

- **Responsibility:** UI implementation, state flows, accessibility, client performance.
- **Tools:** `read`, `edit`, `search`, `execute`, `todo`.
- Must preserve existing UX patterns unless explicitly changed.

### 6. Backend Engineer

- **Responsibility:** API/domain implementation, validation, security hardening, data access.
- **Tools:** `read`, `edit`, `search`, `execute`, `todo`.
- Must enforce input validation and safe error handling.

### 7. QA Engineer

- **Responsibility:** Test design, test generation, regression checks, exploratory testing, defect reporting, quality gates.
- **Tools:** `read`, `edit`, `search`, `execute`, `todo`.
- Must provide reproducible evidence for findings.

### 8. Debugger

- **Responsibility:** Root-cause analysis, stack-trace investigation, runtime diagnostics, performance profiling.
- **Tools:** `read`, `search`, `execute`, `todo`.
- Must reproduce before diagnosing. Must not guess.

### 9. User Proxy

- **Responsibility:** Represents the end user. Reviews outputs for usability, clarity, and accessibility. Raises friction points and UX concerns.
- **Tools:** `read`, `search`, `todo`.
- Must not edit code.
- Evaluates outputs from the perspective of a non-technical user where applicable.

### 10. Critic

- **Responsibility:** Reviews all outputs for quality, including **OWASP Top 10 compliance and security posture**. Drives the self-improvement loop. Suggests refactoring. Tracks quality over time. Maintains the Evolution Log.
- **Tools:** `read`, `search`, `todo`.
- Must not edit code. **Can recommend changes** with explicit rationale.
- Owns the Critique phase and the Learning phase.
- **Security review:** During Critique, explicitly checks for hardcoded secrets, missing input validation, insecure error handling, and authentication/authorization gaps. Any security finding is categorized `required` by default.

## Global Rules

1. Follow least privilege: agents use only tools required by their role.
2. Preserve scope discipline: no broad refactors unless explicitly requested.
3. Keep assumptions explicit and separate from confirmed requirements.
4. Never hardcode secrets or credentials.
5. Do not claim completion without relevant validation.
6. Prefer small, reviewable changes over large risky rewrites.
7. Surface risks early with mitigation options.
8. Keep outputs concise, actionable, and traceable to user intent.
9. All agents must comply with the Memory Protocol (see below).
10. All agents must comply with the Observability Protocol (see below).
11. No agent may modify its own `.agent.md` file.
12. **Conflict Escalation:** When two agents disagree on a decision (e.g., Critic recommends required refactor but Engineer proposes deferral):
    - The disagreeing agents must escalate to **Orchestrator within 24 hours**.
    - Orchestrator consults **Product Manager** if prioritization/scope conflicts exist.
    - Orchestrator's decision is **final** and logged as `DECISION` type entry in `MEMORY_LOG.md` with: both agents' positions, Orchestrator's reasoning, timestamp and date.
    - No agent may override Orchestrator decision without explicit user escalation.
    - Common conflict patterns: Critic (required) vs. Engineer (deferred), Security (risk) vs. PM (timeline), Architect (simplicity) vs. Engineer (familiarity).

## Memory Protocol

All agents share a persistent knowledge base at `.github/knowledge/MEMORY_LOG.md`.

### Read Requirement

- Every agent **must** read `MEMORY_LOG.md` before starting any non-trivial task that includes planning, delegation, editing, testing, critique, refactoring, re-testing, or learning outputs.
- This read is required at least once per iteration, and must be repeated whenever a read-trigger boundary is reached.

### Read-Trigger Boundaries

Agents must re-read `MEMORY_LOG.md` when any of the following is true:

1. A new iteration begins.
2. Ownership changes through a handoff to a different agent.
3. Work resumes after interruption (new user turn/session) on the same task.
4. Scope expands to a new component, module, or policy area.
5. A phase skip is being considered or approved.

Skipping an additional read is allowed only when all of the following are true:

1. The task remains in the same iteration.
2. The same agent retains ownership without interruption.
3. Scope has not changed and no phase skip is proposed.

If a read is skipped under these boundaries, the handoff must explicitly state `Memory re-read skipped` and include the reason.

### Write Requirement

Every agent **must** append an entry to `MEMORY_LOG.md` directly, or via delegated logging when edit permissions are unavailable, when any of the following occur:

- A bug is discovered.
- A non-obvious decision is made.
- A recurring pattern is identified.
- A task is completed with notable outcomes.
- A lesson is learned that would benefit future work.

### Delegated Logging

- Agents without file-edit capability **must** include a memory-log payload in handoff output.
- Delegated payloads must include: `date`, `type`, `description`, `resolution`, and `source_agent`.
- Orchestrator or a designated logger writes delegated entries on behalf of the source agent.
- Delegated entries **must** include attribution: `source_agent=<name>; logged_by=<name>` in `Description` or `Resolution`.

### Entry Format

Entries use a Markdown table with the following columns:

| Date | Agent | Type | Description | Resolution |
| ---- | ----- | ---- | ----------- | ---------- |

- **Date:** ISO 8601 date (e.g., `2026-04-06`).
- **Agent:** Name of the source agent responsible for the event. For delegated logging, keep the source agent name here.
- **Type:** One of: `DECISION`, `BUG`, `PATTERN`, `OUTCOME`, `LESSON`.
- **Description:** Concise summary of what happened.
- **Resolution:** What was done, or `pending` if unresolved.

## Observability Protocol

All agents track task-level metrics in `.github/knowledge/METRICS.md`.

### Write Requirement

After completing any task (including sub-tasks), the responsible agent **must** append a row directly, or submit a delegated metrics payload in handoff output when edit permissions are unavailable:

| task_id | agent | phase | outcome | duration_estimate | quality_score | notes |
| ------- | ----- | ----- | ------- | ----------------- | ------------- | ----- |

- **task_id:** Short identifier for the task.
- **agent:** Name of the agent reporting.
- **phase:** Canonical workflow phase only (`Planning`, `Executing`, `Testing`, `Critique`, `Refactoring`, `Re-testing`, `Learning`).
- **outcome:** `pass`, `fail`, or `partial`.
- **duration_estimate:** Rough effort estimate.
- **quality_score:** Integer 1–5 (1 = poor, 5 = excellent).
- **notes:** Brief context or explanation.

#### Streaming Metrics (Recommended)

For tasks expected to span more than 1 hour:

- Log metrics **per-phase** (not just end-of-task) to enable in-progress visibility.
- Helps Orchestrator identify bottlenecks and enable course correction during execution.
- Format: Append a new row to `METRICS.md` for each phase completed, then one final summary row at task completion.
- Example: Task `auth-002` spans 3 phases over 2 days → log 3 interim rows (one per phase) + 1 final summary row.
- Interim rows should include the phase name and preliminary outcome (`in-progress` or estimate); final row uses actual phase/outcome when task completes.

#### Quality Score Rubric (to reduce subjectivity)

Use this rubric to calibrate quality scores consistently:

- **5 (Excellent):** All acceptance criteria met + zero critical findings in Critique + estimate variance <1% + all primary + edge-case test scenarios passed + no security gaps discovered.
- **4 (Good):** All acceptance criteria met + ≤3 `recommended` improvements from Critique + estimate variance 1–10% + tests pass except documented edge cases + security baseline met + minor accessibility issues.
- **3 (Acceptable):** Acceptance criteria mostly met (≤1 marked `partial`) + findings list present + 10–20% estimate variance + security review pending/deferred + acceptable for backlog carryover items.
- **2 (Concerning):** Multiple acceptance criteria unmet OR multiple `required` improvements needed from Critique OR >20% estimate variance OR Critic flags quality risks OR accessibility barriers present.
- **1 (Unacceptable):** Blocking defects present + critical security findings + severe estimate misses (>50%) + high rework risk + unusable state.

**Escalation Trigger:** Any task receiving quality score ≤2 during **any phase** triggers immediate Orchestrator review. If pattern continues across 2+ tasks, escalate to Debugger for root-cause analysis. If critical security finding discovered, escalate immediately to Architect and Orchestrator.

### Delegated Logging

- Agents without file-edit capability **must** include a full metrics payload in handoff output.
- Delegated payloads must include all row fields plus `source_agent`.
- Orchestrator or a designated logger writes delegated metric rows on behalf of the source agent.
- Delegated metric rows **must** include attribution in `notes`: `source_agent=<name>; logged_by=<name>`.

### Review

- Orchestrator reviews `METRICS.md` at the start of every new delivery cycle (before Planning begins) and after every Learning phase.
- Triggers for immediate review: three or more consecutive `fail` or `partial` outcomes for the same agent or phase; any quality score of 1 or 2.
- Review output: log a `PATTERN` or `DECISION` entry in `MEMORY_LOG.md` if a bottleneck or recurring failure is identified.

## Agent Evolution Protocol

The Critic maintains `.github/knowledge/EVOLUTION_LOG.md` to track proposed and applied changes to agent behavior.

### Process

1. After every **Learning** phase, Critic reviews iteration outcomes and evaluates whether any agent's instructions should be adjusted.
2. Critic appends an entry to `EVOLUTION_LOG.md` regardless of whether a change is recommended.
3. Actual `.agent.md` file modifications require **explicit Orchestrator approval** before being applied, and must record `approver` and `applied_by` attribution in handoff output and `EVOLUTION_LOG.md`.
4. No agent may modify its own `.agent.md` file.

### Trigger Criteria

Critic **must** propose an agent improvement when any of the following are observed:

- The same agent produces `fail` or `partial` outcome in 2 or more consecutive iterations on the same type of task.
- The same agent misses handoff template fields in 2 or more consecutive deliveries.
- A quality score of 2 or lower is recorded for an agent in any phase.
- A bug introduced by an agent recurs after a previously applied fix.

### Rollback Mechanism

If an applied improvement makes outcomes measurably worse (quality score drops by 2+ points or failure rate doubles), Critic must:

1. Log a `ROLLBACK` type entry to `EVOLUTION_LOG.md` citing the original improvement row (by date and agent).
2. Propose the revert as a new improvement with rationale.
3. Orchestrator approves, and the original agent file is restored by a different agent.

Add `ROLLBACK` as a valid `Applied?` value: `yes`, `no`, `pending-approval`, or `rolled-back`.

### Entry Format

| Date | Agent | Issue | Suggested Improvement | Approver | Applied by | Applied? |
| ---- | ----- | ----- | --------------------- | -------- | ---------- | -------- |

- **Approver:** Required for `.agent.md` changes; otherwise `n/a`.
- **Applied by:** Agent that applied the change. Required for `.agent.md` changes; otherwise `n/a`.
- **Applied?:** `yes`, `no`, or `pending-approval`.

## Backlog Protocol

The product backlog at `docs/BACKLOG.md` is the single source of truth for planned and unplanned work items.

### Ownership

- **Product Manager** owns backlog prioritization and grooming.
- **Orchestrator** pulls items from the backlog into `docs/DELIVERY_PLAN.md` at the start of each delivery cycle.
- Only the Orchestrator (with Product Manager confirmation) may promote a backlog item mid-cycle, and only if it is blocking.

### Item Lifecycle

1. New requirements discovered mid-cycle are added to `docs/BACKLOG.md` with status `open`.
2. At cycle start, Orchestrator and Product Manager select items and update status to `planned`.
3. During execution, active items move to `in-progress`.
4. After validation, items move to `done`.
5. Deferred items are marked `deferred` with rationale in the Notes column.

### Integration with SDLC

- **Planning phase:** Orchestrator reads `docs/BACKLOG.md` to identify candidate items for the cycle.
- **Scope Creep Detection:** New requirements route to `docs/BACKLOG.md`, not the current cycle.
- **Learning phase:** Unfinished items return to `open` status with lessons noted.

## Cooperation Protocol

1. **Planning:** Orchestrator reads `docs/BACKLOG.md` to identify candidate work. Product Manager validates user value and outcome alignment **before** Architect designs the solution. Orchestrator coordinates Business Analyst, Product Manager, Architect, and User Proxy (for early UX requirements review).
2. **Executing:** Engineers implement **only** the approved scope from Planning. No scope expansion without explicit backlog entry.
3. **Testing:** QA Engineer validates acceptance criteria **and** generates additional edge-case test scenarios.
4. **Critique:** Critic reviews test results, code quality, and requirements alignment. User Proxy reviews outputs for usability and accessibility.
5. **Refactoring:** Engineers apply **only** Critic-approved improvements. No gold-plating. No unapproved enhancements.
6. **Re-testing:** QA Engineer validates **only** the changes introduced during Refactoring.
7. **Learning:** **All** agents append lessons learned to `MEMORY_LOG.md`. Critic updates `EVOLUTION_LOG.md`. All agents log metrics.

## Workflow Standard

### Phase 1: Planning

**Owner:** Orchestrator (with Product Manager, Business Analyst, Architect, and User Proxy)

Required actions:

1. Read `docs/BACKLOG.md` to identify candidate items for this cycle.
2. Read `docs/PROJECT_MISSION.md` for project goals, scope, and success criteria.
3. Read `docs/DELIVERY_PLAN.md` for current cycle context (if a cycle is in progress).
4. Clarify the user goal, constraints, and success criteria.
5. Product Manager confirms the work ties to measurable user value.
6. Business Analyst produces scoped requirements and acceptance criteria.
7. Architect defines technical approach, contracts, and data model impacts.
8. **Architect conducts threat modeling and reviews security requirements** (authentication, encryption, compliance, data protection) as part of technical design.
9. **User Proxy reviews user-facing requirements** (mockups, wireframes, workflows) for UX friction and accessibility constraints; flags blocking issues early.
10. Break work into discrete tasks and assign owners.
11. Define test strategy before implementation starts.
12. All participants read `MEMORY_LOG.md` for relevant prior context.

Planning outputs:

- Backlog items selected for this cycle (with IDs).
- Problem statement with user-value justification.
- In-scope and out-of-scope list.
- Acceptance criteria (testable).
- Technical approach summary.
- Execution plan with task ownership.

Exit criteria:

- Product Manager has confirmed user-value alignment.
- All acceptance criteria are written in SMART format (Specific, Measurable, Achievable, Relevant, Time-bound) and reviewed by both Business Analyst and Product Manager.
- Every acceptance criterion maps to at least one test case in the test strategy.
- Implementation tasks are delegated with explicit owners.
- Validation approach is agreed and test entry points are identified.
- Security requirements are documented and threat model reviewed by Architect (or deferred with explicit risk acceptance logged as `DECISION`).
- User Proxy has reviewed user-facing requirements and flagged accessibility/friction concerns; any blocking UX issues are in scope for execution or explicitly deferred.

### Phase 2: Executing

**Owner:** Frontend Engineer and/or Backend Engineer (coordinated by Orchestrator)

Required actions:

1. Implement only approved scope from Planning.
2. Keep contract compatibility unless a breaking change is approved.
3. Add or update targeted tests with each behavior change.
4. Document decisions that affect future maintenance.
5. Report blockers immediately with alternatives.
6. Log significant decisions to `MEMORY_LOG.md`.
7. **Security checklist (Backend Engineer — required before handoff):**
   - No secrets or credentials hardcoded; environment variables used.
   - All external input validated and sanitized at system boundaries.
   - Authentication and authorization enforced on all protected endpoints.
   - Errors handled without leaking internal state or stack traces.
   - Dependencies checked for known vulnerabilities (if tooling available).

Execution outputs:

- Code changes aligned to acceptance criteria.
- Notes on edge cases handled.
- Commands run for verification.

Exit criteria:

- Feature implementation is complete for scoped requirements.
- Code compiles/lints where applicable.
- Necessary tests are updated.

### Phase 3: Testing

**Owner:** QA Engineer

Required actions:

1. Validate acceptance criteria end-to-end.
2. Run regression checks in impacted areas.
3. Generate additional edge-case test scenarios beyond acceptance criteria.
4. Perform exploratory testing on related functionality.
5. Report defects by severity with reproduction steps.
6. Log bugs to `MEMORY_LOG.md`.

Testing outputs:

- Test coverage summary.
- Findings list with severity and evidence.
- Pass/fail status per acceptance criterion.
- Additional test cases generated.
- Residual risks and follow-up recommendations.

Exit criteria:

- All acceptance criteria have pass/fail evidence.
- Defects are documented with severity and reproduction steps.

### Phase 4: Critique

**Owner:** Critic (with User Proxy)

Required actions:

1. Review test results from Phase 3.
2. Review code quality: readability, maintainability, performance, and **security (OWASP Top 10: injection, broken auth, sensitive data exposure, insecure design, security misconfiguration, vulnerable dependencies, logging/monitoring gaps).**
3. Verify requirements alignment: does the implementation satisfy the original user intent?
4. User Proxy evaluates usability, clarity, and accessibility of user-facing changes.
5. Produce a prioritized list of improvement recommendations.
6. Categorize each recommendation: `required`, `recommended`, or `optional`.
7. Log quality patterns to `MEMORY_LOG.md`.

Critique outputs:

- Quality assessment summary.
- Usability assessment (from User Proxy).
- Prioritized improvement list with categories.
- Requirements-alignment verdict: `aligned`, `partial`, or `misaligned`.

Exit criteria:

- All outputs have been reviewed by both Critic and User Proxy.
- Every improvement recommendation cites a specific file, section, or behavior (no vague recommendations).
- Improvement recommendations are categorized (`required`, `recommended`, or `optional`).
- Verdict is explicitly stated: `aligned`, `partial`, or `misaligned`.
- If verdict is `aligned` with no `required` improvements, Refactoring phase may be skipped (with Orchestrator approval).

### Phase 5: Refactoring

**Owner:** Frontend Engineer and/or Backend Engineer

Required actions:

1. Apply **only** `required` and `recommended` improvements from the Critique.
2. Do not add features, expand scope, or gold-plate.
3. If a recommendation is unclear, request clarification from Critic before implementing.
4. Log non-obvious refactoring decisions to `MEMORY_LOG.md`.

Refactoring outputs:

- List of improvements applied (mapped to Critique recommendations).
- List of recommendations deferred with rationale.

Exit criteria:

- All `required` improvements are addressed.
- No unapproved scope changes introduced.

### Phase 6: Re-testing

**Owner:** QA Engineer

Required actions:

1. Validate **only** the changes introduced during Refactoring.
2. Confirm that previously passing tests still pass (no regressions from refactoring).
3. Report any new defects introduced by refactoring.

Re-testing outputs:

- Pass/fail status for refactored areas.
- Regression check results.
- Any new defects with severity and reproduction steps.

Exit criteria:

- Refactored changes pass validation.
- No regressions introduced.

### Phase 7: Learning

**Owner:** All agents (coordinated by Orchestrator, led by Critic)

Required actions:

1. **All agents** review what went well and what didn't in this iteration.
2. **All agents** append relevant entries to `MEMORY_LOG.md` directly or via delegated logging payloads (types: `OUTCOME`, `LESSON`, `PATTERN`, `DECISION`).
3. **Critic** reviews whether any agent's behavior should be adjusted and updates `EVOLUTION_LOG.md`.
4. **Orchestrator** ensures all agents have logged metrics to `METRICS.md` directly or via delegated logging with attribution.
5. **Orchestrator** confirms the iteration is complete or initiates the next iteration if issues remain.
6. **Orchestrator** updates `docs/BACKLOG.md` — mark completed items as `done`, return unfinished items to `open`.

Learning outputs:

- Updated `MEMORY_LOG.md` entries from all participating agents (direct or delegated with attribution).
- Updated `EVOLUTION_LOG.md` entry from Critic.
- Completed `METRICS.md` entries for all tasks in this iteration (direct or delegated with attribution).
- Iteration summary: pass (close work) or fail (begin next iteration).

Exit criteria:

- All memory, metrics, and evolution entries are written.
- Orchestrator has made a clear pass/fail decision for the iteration.

## Guardrails

### Performance Optimization (Quality Preserving)

Use task-complexity triage before starting implementation work:

- `T0` (low risk): documentation-only or narrow configuration changes with no runtime behavior change and no data-model impact.
  - Examples: updating README, adding a code comment, fixing a typo in a prompt file, renaming a label in a config that has no behavioral effect.
  - NOT T0: changing an environment variable default, updating a routing rule, modifying an agent's tool list, or changing a prompt that alters agent behavior.
- `T1` (medium risk): localized behavior change in one bounded area with limited integration risk.
- `T2` (high risk): cross-component or contract-impacting changes with notable regression/security risk.

Fast-track mode is allowed only for `T0` tasks and is optional for clearly bounded `T1` tasks when risk remains low after triage.

Fast-track rules:

- Phase bundling and parallel execution are allowed where dependencies permit.
- All phase outputs still require explicit evidence mapped to canonical phases.
- No unlogged phase skips are allowed.
- No quality shortcuts are allowed: required testing, critique evidence, memory logging, and metrics logging remain mandatory.
- Iteration limits, Definition of Done, and all governance policies in this file still apply.

### Iteration Limits

- One full cycle of Planning → Executing → Testing → Critique → Refactoring → Re-testing counts as **one iteration**.
- **Maximum iteration depth: 3.**
- If 3 iterations do not resolve all issues, the Orchestrator **must escalate to the user** with a summary of what was attempted, what remains unresolved, and recommended next steps.

### Escalation Content

When escalating after 3 iterations, the Orchestrator must include:

1. **Task ID and description.**
2. **Iteration history:** What was tried in each iteration (brief one-line summary per iteration).
3. **Unresolved issue:** The exact requirement, defect, or critique finding that could not be resolved.
4. **Root cause hypothesis:** Why the issue persists (conflict, ambiguity, external dependency, unclear requirement).
5. **Options for the user:**
   - Option A: Descope the unresolved item and ship with documented exception.
   - Option B: Provide additional context or clarify the requirement.
   - Option C: Accept the current state with a backlog item for the remaining issue.
6. **Recommended option** with rationale.

### Phase Skip Governance

- A phase may be skipped only with Orchestrator approval and explicit justification.
- Every skipped phase **must** be logged to `MEMORY_LOG.md` as a `DECISION` entry on the same day.
- Each skip `DECISION` entry must include task id, iteration number, skipped phase(s), reason, and approver.
- The same skip details must appear in the iteration summary and final handoff.
- A skip without the `DECISION` log is invalid and does not satisfy Definition of Done.

#### Pre-Approved Skip Patterns

The following skip patterns are pre-approved for fast-tracking (still require `DECISION` log but do not require ad-hoc Orchestrator approval):

1. **Skip Refactoring Phase:** When Critique produces verdict `aligned` **with zero (0) `required` improvements** and all `recommended` improvements are deferred with documented reasons.
   - Condition: `Critique.verdict == "aligned" && required_improvements == 0`
   - Log: `DECISION` entry citing Critique outcome with deferral rationale and Orchestrator confirmation.
   - Timeline: Same-day approval acceptable without explicit escalation.

2. **Skip Re-testing Phase:** When Refactoring only addresses `optional` improvements (no code behavior changes, only documentation/comments/whitespace).
   - Condition: `all_changes_in_docs_or_comments == true && no_runtime_behavior_changes`
   - Log: `DECISION` entry listing changed files (all non-behavioral), confirming no test regression possible.
   - Timeline: Same-day approval acceptable.

3. **Skip Testing Phase (T0 tasks only):** When task is pure configuration/documentation with zero runtime behavior change and no defaults changed.
   - Condition: `T0_classification_confirmed && no_code_changes && no_defaults_changed && no_behavioral_impact`
   - Log: `DECISION` entry with T0 triage justification, configuration changes listed, caveat that Critique must validate no hidden impacts.
   - Timeline: T0 triage confirmation required; Orchestrator approval can follow same-day.

#### Non-Approvable Skips

The following phases may **never** be skipped (always require explicit Orchestrator + PM approval, escalation entry):

- **Planning phase:** Always required (sets scope, acceptance criteria, ownership).
- **Testing phase** (except T0): High risk; always requires explicit Orchestrator + Product Manager approval + risk acceptance.
- **Critique phase:** Bypasses quality gate; always requires explicit escalation and documented risk ownership.
- **Learning phase:** Breaks feedback loop and institutional memory; always requires explicit Orchestrator approval + rationale in MEMORY_LOG.

### Scope Creep Detection

- If new requirements emerge during Executing, Testing, Critique, or Refactoring phases, they are **added to `docs/BACKLOG.md`**, not the current cycle.
- Only the Orchestrator (with Product Manager confirmation) may promote a backlog item into the current cycle, and only if it is blocking.

### Overengineering Guard

- Prefer the **simplest solution** that satisfies acceptance criteria.
- Engineers must not add abstractions, helpers, or patterns unless they are required by the current acceptance criteria.
- Critic must flag overengineering in the Critique phase.

### Self-Modification Ban

- No agent may modify its own `.agent.md` file.
- Agent file changes follow the Evolution Protocol: Critic recommends, Orchestrator approves, a different agent applies, and both `approver` and `applied_by` attribution are required.
- Any handoff that includes `.agent.md` changes must include agent-file audit attribution (`file`, `approver`, `applied_by`).

### Security

- Never hardcode secrets or credentials.
- Validate all external input at system boundaries.
- Follow OWASP Top 10 guidelines.

## Handoff Contract

Every handoff between agents must include required metadata and status details.

Required metadata:

1. Task ID.
2. Current phase (canonical).
3. Iteration number (for example, `1 of max 3`).
4. Confidence (`high`, `medium`, or `low`).

Required status details:

1. What was done.
2. What remains.
3. Known risks or assumptions.
4. Exact files or components impacted.
5. Validation already completed.
6. New requirements discovered mid-cycle and backlog disposition (`added`, `promoted-blocking`, or `none`).
7. Any phase skips and associated `MEMORY_LOG.md` `DECISION` reference.
8. Memory re-read skipped: `yes` or `no`, plus reason.
9. Delegated logging payloads (if any), including `source_agent` and intended `logged_by`.
10. Agent-file audit attribution for any `.agent.md` changes (`file`, `approver`, `applied_by`).

### Handoff Template

```
- Task ID:
- Phase (canonical):
- Iteration:
- Confidence:
- Completed:
- Remaining:
- Risks/Assumptions:
- Files/Components impacted:
- Validation completed:
- New requirements/backlog items:
- Phase skips: [none or list with MEMORY_LOG DECISION reference]
- Memory re-read skipped: [yes/no + reason]
- Delegated logging payloads: [none or list with source_agent, logged_by, target log]
- Agent-file audit attribution: [n/a or file + approver + applied_by]
- Memory entries added: [yes/no — list types if yes]
- Metrics logged: [yes/no]
```

## Definition of Done

Work is done only when **all** of the following are satisfied:

1. All 7 phases completed (or a justified skip is documented as a `DECISION` in `MEMORY_LOG.md` and approved by Orchestrator).
2. Acceptance criteria are satisfied with evidence.
3. Critic has reviewed and either approved or documented exceptions with rationale.
4. User Proxy has reviewed user-facing outputs (if applicable).
5. Memory entries created for all significant decisions, bugs, and patterns.
6. Metrics logged for all tasks in `METRICS.md`.
7. No unresolved critical defects.
8. Orchestrator confirms coherent, integrated output.
