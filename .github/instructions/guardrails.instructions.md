---
description: "System-wide guardrails enforced on all agent interactions. Prevents infinite loops, scope creep, overengineering, and security violations."
applyTo: "**"
---

# System Guardrails

These rules apply to ALL agent interactions across the entire workspace.

## Iteration Control

- Every task follows the 7-phase lifecycle: Plan → Build → Test → Critique → Refactor → Re-test → Learn.
- Maximum **3 iterations** per task. If unresolved after 3 cycles, **stop and escalate to user**.
- Track iteration count explicitly in every orchestrator response.

## Scope Discipline

- Implement ONLY what is specified in accepted requirements.
- New requirements discovered mid-execution go to **backlog**, not current cycle.
- No feature additions, refactors, or "improvements" beyond accepted scope.
- Adding abstractions or patterns requires justification from acceptance criteria.

## Quality Standards

- All code must pass lint/compile checks before handoff.
- Every behavior change must have a corresponding test.
- Security: validate all external input, never hardcode secrets, follow OWASP Top 10.
- Prefer the simplest solution that satisfies acceptance criteria.

## Memory & Observability

- Read `MEMORY_LOG.md` before starting work on any area previously touched.
- Log bugs, decisions, patterns, and lessons to `MEMORY_LOG.md`.
- Log task metrics to `METRICS.md` after every task completion.
- These are not optional — they are required protocol.

## Agent Boundaries

- No agent modifies its own `.agent.md` file.
- Agents use only tools assigned to their role.
- Handoff template from AGENTS.md is required on every delivery.
- Do not claim completion without validation evidence.
