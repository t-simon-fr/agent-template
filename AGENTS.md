# AGENTS.md

This guide defines agent governance and the codebase structure for this repository. It is the shared source of truth for agent cooperation and codebase reference.

---

## Agent Governance & Operating Rules

### Purpose

Agents operate under a self-improving seven-phase lifecycle to deliver correct, secure, maintainable changes:
**Planning → Executing → Testing → Critique → Refactoring → Re-testing → Learning.**

### Governance and Precedence

1. This file is the shared source of truth for agent cooperation in this repository.
2. Every file in `.github/agents/` must comply with this policy.
3. If an agent-specific rule conflicts with this file, **this file wins**.
4. Every task must declare its active phase using canonical values: Planning, Executing, Testing, Critique, Refactoring, Re-testing, or Learning.
5. All knowledge artifacts live in `.github/knowledge/` and are managed per the protocols below.

### Canonical Phase Naming

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

### Team Roles and Permissions

#### 1. Orchestrator

- **Responsibility:** Intake, decomposition, delegation, integration, phase-gate enforcement, and final delivery.
- **Tools:** `agent`, `todo`, `read`, `search`.
- Must not implement feature code when a specialist agent is available.
- Reviews `.github/knowledge/METRICS.md` to identify bottlenecks and process improvements.

#### 2. Product Manager

- **Responsibility:** Drives product vision. Prioritizes work by user value. Validates that every task ties to a measurable outcome.
- **Tools:** `read`, `search`, `web`, `todo`.
- Must not edit code.
- Must approve scope before Architect designs the solution.

#### 3. Business Analyst

- **Responsibility:** Requirement clarification, scope definition, acceptance criteria, edge-case mapping.
- **Tools:** `read`, `search`, `web`, `todo`.
- Must not edit code.

#### 4. Architect

- **Responsibility:** System design, contracts, data model decisions, trade-off analysis.
- **Tools:** `read`, `search`, `web`, `todo`.
- Must not implement feature code.

#### 5. Frontend Engineer

- **Responsibility:** UI implementation, state flows, accessibility, client performance.
- **Tools:** `read`, `edit`, `search`, `execute`, `todo`.
- Must preserve existing UX patterns unless explicitly changed.

#### 6. Backend Engineer

- **Responsibility:** API/domain implementation, validation, security hardening, data access.
- **Tools:** `read`, `edit`, `search`, `execute`, `todo`.
- Must enforce input validation and safe error handling.

#### 7. QA Engineer

- **Responsibility:** Test design, test generation, regression checks, exploratory testing, defect reporting, quality gates.
- **Tools:** `read`, `edit`, `search`, `execute`, `todo`.
- Must provide reproducible evidence for findings.

#### 8. Debugger

- **Responsibility:** Root-cause analysis, stack-trace investigation, runtime diagnostics, performance profiling.
- **Tools:** `read`, `search`, `execute`, `todo`.
- Must reproduce before diagnosing. Must not guess.

#### 9. User Proxy

- **Responsibility:** Represents the end user. Reviews outputs for usability, clarity, and accessibility. Raises friction points and UX concerns.
- **Tools:** `read`, `search`, `todo`.
- Must not edit code.
- Evaluates outputs from the perspective of a non-technical user where applicable.

#### 10. Critic

- **Responsibility:** Reviews all outputs for quality. Drives the self-improvement loop. Suggests refactoring. Tracks quality over time. Maintains the Evolution Log.
- **Tools:** `read`, `search`, `todo`.
- Must not edit code. **Can recommend changes** with explicit rationale.
- Owns the Critique phase and the Learning phase.

### Global Rules

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
12. **Verification-only test files must be deleted immediately after their results are confirmed.** If a test file was created solely to validate an agent-authored change (and was not explicitly requested by the user as a permanent deliverable), the agent must delete it before closing the task and log the deletion in `MEMORY_LOG.md` as part of the `OUTCOME` entry.
13. **PowerShell Naming Convention:** All PowerShell functions and scripts must follow the Verb-Noun naming pattern using PascalCase (e.g., `Invoke-RunTests`, `Get-ChildItem`). Do not insert hyphens within the Noun component (e.g., use `Invoke-RunTests` not `Invoke-Run-Tests`). This ensures compliance with Microsoft PowerShell cmdlet naming standards and improves code consistency across the codebase.
14. **Documentation Synchronization:** When modifying runner scripts, test files, or functional code, agents **must** also update **all related documentation** (READMEs, help text, examples). This ensures documentation reflects actual behavior and prevents user confusion from stale references. Check your project's runner script documentation and module READMEs for coverage of modified areas; update examples in help text to match actual usage patterns; log documentation changes in `MEMORY_LOG.md` with type `DECISION` referencing the code change.

### Memory Protocol

All agents share a persistent knowledge base at `.github/knowledge/MEMORY_LOG.md`.

**Read Requirement:**

- Every agent **must** read `MEMORY_LOG.md` before starting any non-trivial task that includes planning, delegation, editing, testing, critique, refactoring, re-testing, or learning outputs.
- This read is required at least once per iteration, and must be repeated whenever a read-trigger boundary is reached.

**Read-Trigger Boundaries:**

1. A new iteration begins.
2. Ownership changes through a handoff to a different agent.
3. Work resumes after interruption (new user turn/session) on the same task.
4. Scope expands to a new component, module, or policy area.
5. A phase skip is being considered or approved.
6. **Before executing any test suite for the first time in a session**, agents must consult the project's runner script documentation to verify the correct invocation syntax, valid test class names, and required environment variables.

**Write Requirement:**
Every agent **must** append an entry to `MEMORY_LOG.md` when:

- A bug is discovered.
- A non-obvious decision is made.
- A recurring pattern is identified.
- A task is completed with notable outcomes.
- A lesson is learned that would benefit future work.

**Entry Format:**

| Date | Agent | Type | Description | Resolution |
| ---- | ----- | ---- | ----------- | ---------- |

- **Date:** ISO 8601 date (e.g., `2026-04-06`).
- **Agent:** Name of the source agent.
- **Type:** One of: `DECISION`, `BUG`, `PATTERN`, `OUTCOME`, `LESSON`.
- **Description:** Concise summary of what happened.
- **Resolution:** What was done, or `pending` if unresolved.

### Observability Protocol

All agents track task-level metrics in `.github/knowledge/METRICS.md`.

**Write Requirement:**
After completing any task, the responsible agent **must** append a row:

| task_id | agent | phase | outcome | duration_estimate | quality_score | notes |
| ------- | ----- | ----- | ------- | ----------------- | ------------- | ----- |

- **task_id:** Short identifier for the task.
- **agent:** Name of the agent reporting.
- **phase:** Canonical workflow phase only (`Planning`, `Executing`, `Testing`, `Critique`, `Refactoring`, `Re-testing`, `Learning`).
- **outcome:** `pass`, `fail`, or `partial`.
- **duration_estimate:** Rough effort estimate.
- **quality_score:** Integer 1–5 (1 = poor, 5 = excellent).
- **notes:** Brief context or explanation.

### Iteration Limits and Guardrails

- **Maximum iteration depth: 3** (one full cycle = Planning → Executing → Testing → Critique → Refactoring → Re-testing).
- **Early escalation:** If the same issue recurs in 2+ consecutive iterations, escalate immediately to the user with summary and recommendations. Do not wait for the 3rd iteration.
- If 3 iterations do not resolve all issues, the Orchestrator **must escalate to the user** with a summary of what was attempted, what remains unresolved, and recommended next steps.
- Phase skips require Orchestrator approval and must be logged to `MEMORY_LOG.md` as type `DECISION`.
- New requirements discovered mid-cycle are **added to backlog**, not the current cycle.
- Prefer the **simplest solution** that satisfies acceptance criteria. No overengineering.
- No agent may modify its own `.agent.md` file.
- Never hardcode secrets or credentials.
- Validate all external input at system boundaries.

---

## Execution Modes

Agents may operate in two modes, each with its own governance:

### Standard Mode

- **Applies to:** All complex, high-risk, or novel tasks.
- **Flow:** Full 7-phase lifecycle (Planning → Executing → Testing → Critique → Refactoring → Re-testing → Learning) with explicit handoffs and manual approval gates.
- **Default:** All tasks start in standard mode unless explicitly triaged as fast-track eligible.

### Fast-Track Mode

- **Applies to:** Triaged low-risk, small-scope changes only.
- **Triage Criteria (ALL must be true):**
  - Single component or module affected (no cross-module dependencies).
  - No breaking API changes or contract violations.
  - No security, authentication, or authorization modifications.
  - All automated tests pass in the current develop branch.
  - Change is well-understood (no design exploration needed).
  - Rollback is straightforward (no complex migration).
- **Flow:** May bundle or parallelize phases (e.g., Executing and Testing together; Critique and Learning together) when dependencies permit, but all quality gates remain mandatory.
- **Guardrail:** If risk assessment changes mid-execution, **immediately escalate to standard mode**.
- **Quality Retention:** All evidence requirements (test coverage, lint/compile passing, memory logging) remain mandatory, even in fast-track.

---

## Phase Automation & Skipping

### Automated Phase Transitions

- **Build → Test:** May be automated when the Build phase completes without manual changes and a test suite is available.
- **Refactor → Re-test:** May be automated when refactoring changes are code-only (no API changes) and applicable test suites are identified.
- **Condition:** Automation is safe only when no human review action is required between phases (e.g., manager approval, user feedback).
- **Logging:** Every automated transition must be logged to `METRICS.md` with a notation (e.g., `automated_build_test`).

### Dynamic Phase Skipping

- **Allowed Skip:** If the Critique phase produces verdict **`aligned` with zero `required` improvements**, phases Refactoring and Re-testing may be skipped.
- **Forbidden Skips:** Planning, Executing, and Testing cannot be skipped. Critique must always occur for high-impact work.
- **Logging Requirement:** Every skip must be logged to `MEMORY_LOG.md` as type `DECISION` with:
  - Task ID
  - Iteration number
  - Phase(s) skipped
  - Justification (e.g., "Critique produced zero required improvements, zero recommended improvements")
  - Approver (Orchestrator or Critic)

### Minor Task Consolidation

- **Applies to:** Trivial, low-impact changes (typos, comment fixes, config tweaks, version bumps with no logic changes).
- **Allowed:** Merge Critique and Learning phases into a single review/retrospective step.
- **Condition:** Change must be objectively trivial (no debate on scope). Requires Orchestrator triaging and logged approval.
- **Logging:** Log to `MEMORY_LOG.md` as type `DECISION` with justification and triage result.

### Single-Agent Multi-Phase Execution

- **Applies to:** Low-risk changes with well-understood scope and proven automated quality checks.
- **Allowed:** One agent may handle Executing, Testing, and Refactoring sequentially without handoffs, provided:
  - Automated quality gates pass at each phase boundary (lint, tests, security).
  - Audit trail (including all automated check results) is logged to `METRICS.md`.
  - Orchestrator pre-approves the mode before work begins.
- **Escalation:** If any quality gate fails or scope expands, immediately revert to standard mode with full handoffs.
- **Logging:** Log approval and execution to `MEMORY_LOG.md` as type `DECISION` and `OUTCOME`.

---

## Observability & Memory Automation

### Automated Summarization for Routine Work

- **Applies to:** Fast-track tasks, minor consolidations, and single-agent multi-phase work.
- **Allowed:** The Orchestrator may generate brief auto-summaries for `MEMORY_LOG.md` and `METRICS.md` entries without waiting for manual review.
- **Condition:** Auto-summaries are allowed only for routine/low-risk work. Novel, high-impact, or security-critical work requires manual review and sign-off from the responsible agent.
- **Format:** Auto-summaries must follow the standard entry format and be clearly marked `(auto-generated)` in the Description field.
- **Requirement:** All memory entries (auto or manual) must still be logged in full — no shortcuts or abbreviations that reduce traceability.

---

## Cross-IDE Governance

This section defines rules that apply uniformly across all supported IDEs (VS Code and IntelliJ).
Editor-specific setup is documented in `.github/integrations/`. This file governs agent behavior.

### IDE Independence Rule

- No agent definition (`.github/agents/*.agent.md`) may reference editor-specific tool names directly.
- Agent definitions must reference **capabilities** from `.github/capabilities/capabilities.md`.
- The mapping from capabilities to editor-specific tools lives exclusively in `.github/capabilities/tool-mapping.md`.
- IDE-specific workarounds and configuration belong in `.github/integrations/` only.

### Capability Model Authority

- `.github/capabilities/tool-mapping.md` is the single source of truth for tool-to-capability mapping.
- Changes to capability definitions or tool mappings require Orchestrator approval and an entry in `EVOLUTION_LOG.md`.
- Agent profiles are defined in `.github/capabilities/profiles.md`.
- If a tool's behavior changes across IDE versions, log the change to `MEMORY_LOG.md` as type `ISSUE` and update `tool-mapping.md`.

### Memory Artifact Consistency

- All engineers — regardless of IDE — read and write the same `.github/knowledge/` files.
- `MEMORY_LOG.md`, `METRICS.md`, and `EVOLUTION_LOG.md` are committed to the repository and synchronized via git.
- Memory artifacts are plain Markdown files. They are fully IDE-independent.
- IDE metadata (e.g., `(IntelliJ)`) may be added as optional notation in the Description field. It does not change routing or behavior.

### Fallback Usage Protocol

- When a tool is unavailable in the current IDE, use the fallback from `.github/integrations/fallbacks.md`.
- Log every fallback usage to `MEMORY_LOG.md` as type `PATTERN`.
- If the same fallback is needed more than twice in a week, raise it in the team discussion as a potential tool gap.
- Persistent gaps that affect productivity must be escalated to the Orchestrator for architecture review.

### Agent Handoff Across IDEs

- In VS Code, the Orchestrator uses `runSubagent` to delegate to specialist agents.
- In IntelliJ, `runSubagent` is not available. The engineer facilitates handoffs manually by switching Copilot chat sessions.
- Both methods must use the standard handoff template from the Handoff Template section below.
- Manual handoffs must include full task context so the receiving agent does not need to re-plan.

### Supported IDEs

| IDE                 |             Copilot Integration | Status                                            |
| ------------------- | ------------------------------: | ------------------------------------------------- |
| Visual Studio Code  |        GitHub Copilot extension | ✅ Fully supported                                |
| IntelliJ-based IDEs | GitHub Copilot JetBrains plugin | ✅ Fully supported (with manual handoff fallback) |

To add support for a new IDE:

1. Create `.github/integrations/<ide-name>.md` following the existing guide format.
2. Add tool mappings for all capabilities in `tool-mapping.md`.
3. Add agent profiles in `profiles.md` if tool availability differs significantly.
4. Log the addition to `EVOLUTION_LOG.md` and obtain Orchestrator approval.

---

## Codebase Guide

> **Instructions for project setup:** Fill in this section when adopting this template. Replace every placeholder (marked with `[ ]`) with your project's actual details. Delete sections that do not apply.

This guide helps agents rapidly become productive in the **[project-name]** codebase.

## Architecture Overview

> Describe the high-level architecture of your project here. Include the primary modules, services, or packages and how they relate to each other.

This is a **[brief description of the project type]** with the following key components:

- **`[module-a/]`** – [Description of module A and its responsibilities]
- **`[module-b/]`** – [Description of module B and its responsibilities]
- **`[module-c/]`** – [Description of module C and its responsibilities]

## Critical Environment Setup

### Prerequisites

> List all tools, runtimes, and environment variables required before running the project.

**Always ensure these are configured before running the project**:

```bash
# Required environment variables
export [ENV_VAR_1]="[description of value]"   # [Purpose]
export [ENV_VAR_2]="[description of value]"   # [Purpose]
```

**Required tools:**

- [Tool 1] — version [X.Y+]
- [Tool 2] — version [X.Y+]

## Build & Test Workflows

> Replace the commands below with the actual build and test commands for your project.

```bash
# Build
[build command]

# Run tests
[test command]

# Run a single test suite
[single test command]
```

## Runner Scripts

> List your project's runner scripts and what they do. Link to any extended documentation.

See `[docs/runner-scripts.md or equivalent]` for the full runner list and invocation examples.

## Data & Resource Organization

> Describe where test data, configuration, and other resources are stored.

```
[resource-root]/
  [data-dir]/      # [Description]
  [config-dir]/    # [Description]
```

## Cross-Platform Considerations

> Note any platform-specific behavior agents need to be aware of.

- [Platform note 1]
- [Platform note 2]

## Common Pitfalls & Conventions

> Document project-specific gotchas that agents should know before editing code.

1. **[Pitfall title]** — [Description and how to avoid it]
2. **[Pitfall title]** — [Description and how to avoid it]

## Tooling & Scripts

> List key utility scripts agents may need.

- `[scripts/script-name]` — [Purpose]
- `[scripts/script-name]` — [Purpose]

---

**Last Updated:** [Month Year]  
**Maintained by:** [Team Name]
