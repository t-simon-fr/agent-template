---
description: "System-wide guardrails enforced on all agent interactions. Prevents infinite loops, scope creep, overengineering, and security violations."
applyTo: "**"
---

# System Guardrails

## Tool Invocation Transparency

- **Mandatory Explanation:** Before using any tool (especially running commands or editing files), every agent must log and clearly explain what will be done and why.
- **Explanation Schema:** Every tool invocation must include:
  1. **Intent:** What is the specific goal?
  2. **Action:** The exact command or file change.
  3. **Technical Breakdown:** Detailed explanation of every flag, parameter, or logic block.
  4. **Risk Level:** Assessment (Low/Medium/High) based on system impact.
- **Fluid Execution:** For read-only tasks (research, analysis) or low-risk work, provide the explanation as a "header" and proceed to the execution results in a single, continuous response turn.
- **Sequential Delivery:** The explanation MUST be provided before or alongside the code block. Do not provide executable code without the required breakdown.

## Iteration Control

- Every task follows the 7-phase lifecycle: **Plan → Build → Test → Critique → Refactor → Re-test → Learn**.
- **Phase Announcement:** Every response must begin with a status update: `[Current Phase: X] | [Iteration: Y/3]`.
- Maximum **3 iterations** per task. If unresolved after 3 cycles, **stop and escalate to user**.
- **Early escalation:** If same issue recurs in 2+ consecutive iterations, escalate immediately with summary and recommendations.

## Execution Modes

- **Standard mode:** Full 7-phase lifecycle with normal handoffs for all tasks.
- **Fast-track mode:** Allowed for triaged low-risk, small-scope work; may bundle or parallelize phases when dependencies permit, but all quality gates and evidence requirements remain mandatory.
- Triage criteria for fast-track: single component change, no breaking API changes, no security/auth modifications, all tests pass in develop, no cross-module dependencies.

## Scope Discipline

- Implement ONLY what is specified in accepted requirements.
- New requirements discovered mid-execution go to **backlog**, not current cycle.
- No feature additions, refactors, or "improvements" beyond accepted scope.
- Adding abstractions or patterns requires justification from acceptance criteria.

## Quality Standards

- **No Command Aliases:** All PowerShell commands must use full Cmdlet names (e.g., `Get-ChildItem` instead of `gci`, `Remove-Item` instead of `rm`) to ensure transparency and readability.
- All code must pass lint/compile checks before handoff.
- Every behavior change must have a corresponding test.
- Security: validate all external input, never hardcode secrets, follow OWASP Top 10.
- Prefer the simplest solution that satisfies acceptance criteria.

## Test Discovery & Integration

- **Runner Script Integration:** When adding a new test class, agents **must** also update all relevant runner scripts to expose the new test.
- **Reference Documentation:** Before creating a new test or updating runner scripts, consult the project's runner script documentation to understand the existing patterns and supported invocations for each test framework.
- **Discrepancy Resolution:** If a test class exists but is not exposed by any runner script in the project's runner documentation, treat it as a bug and integrate it immediately.
- **Validation:** After updating runner scripts, verify that syntax is correct and the test class is referenced accurately. Update the runner script documentation if new options or invocations are added.

## Phase Automation & Skipping

- **Standing Approval:** Agents are pre-approved to auto-advance from **Plan → Build → Test** without manual user intervention for read-only analysis or low-impact changes that do not involve data deletion or external costs.
- **Final Result Rule:** A 'Plan' is a status update, not the final deliverable. Every interaction must aim to complete the 'Build' phase (execution) and provide results before ending the response. Ending a response on 'Planning' is considered an incomplete task.
- **Safe phase automation:** Automate transitions Build → Test and Refactor → Re-test when no manual intervention is needed.
- **Dynamic phase skipping:** If Critique phase produces verdict `aligned` with zero `required` improvements, phases Refactoring and Re-testing may be skipped.

## Memory & Observability

- Read `.github/knowledge/MEMORY_LOG.md` before starting work on any area previously touched.
- Log bugs, decisions, patterns, and lessons to `.github/knowledge/MEMORY_LOG.md`.
- Log task metrics to `.github/knowledge/METRICS.md` after every task completion.
- **Automated summarization:** For routine/low-risk work, Orchestrator may generate brief auto-summaries for MEMORY_LOG and METRICS entries.

## Agent Boundaries

- No agent modifies its own `.agent.md` file.
- Agents use only tools assigned to their role.
- Handoff template from AGENTS.md is required on every delivery.
- Do not claim completion without validation evidence.
