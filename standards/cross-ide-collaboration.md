# Cross-IDE Collaboration Protocol

This document defines how engineers using different IDEs (VS Code and IntelliJ)
coordinate within the same AI-assisted development workflow.

The core principle: **IDE choice is invisible to the workflow.**
All agent behavior, governance, memory, and phase cycles are shared and IDE-independent.

---

## Fundamental Rules

1. **Repository is the source of truth.** All decisions, agent behavior, and knowledge
   artifacts live in `.github/`. IDE-specific configuration is minimal and only lives
   in `.vscode/` or `.idea/`.

2. **No IDE-specific agent definitions.** Agent definitions in `.github/agents/` must
   reference capabilities from `.github/capabilities/`, not editor-specific tools.
   IDE-specific tool mappings belong in `.github/capabilities/tool-mapping.md` only.

3. **Memory artifacts are shared.** All engineers — regardless of IDE — read and write
   to the same `.github/knowledge/MEMORY_LOG.md`, `METRICS.md`, and `EVOLUTION_LOG.md`.
   These files are committed to the repository and synchronized via git.

4. **Phases apply equally.** The 7-phase lifecycle (Planning → Executing → Testing →
   Critique → Refactoring → Re-testing → Learning) applies to all engineers in all IDEs.
   IDE choice never justifies skipping or modifying phases.

5. **Governance is IDE-independent.** Rules in `AGENTS.md`, guardrails in
   `.github/instructions/`, and this document apply equally to all IDEs.

---

## Shared Memory & Knowledge Artifacts

### Format

Memory entries may optionally include the IDE as metadata for observability, but
this never changes routing, workflow, or phase behavior:

```
| 2026-05-08 | backend-engineer | DECISION | (IntelliJ) Use project wrapper script instead of direct CLI invocation | Ensures consistent toolchain behavior across OS |
```

The `(IntelliJ)` or `(VS Code)` notation in the Description field is optional and informational only.

### Synchronization

- All memory artifacts are tracked in git like any other file.
- After completing work, **commit and push** `.github/knowledge/` changes to the branch.
- Before starting work, **pull** latest changes to get team memory updates.
- If two engineers append entries simultaneously, resolve merge conflicts by concatenating entries.

### Memory Read Requirement

Regardless of IDE, all agents must read `MEMORY_LOG.md` before any non-trivial task.
If the agent cannot read the file (unusual tool failure), the engineer must paste
the relevant recent entries into the chat manually.

---

## Mixed-IDE Workflow Example

**Scenario:** Alice uses VS Code; Bob uses IntelliJ. They are working on the same feature.

```
Day 1 — Alice (VS Code)
  1. Alice invokes @orchestrator: "Plan a new [feature or test]"
  2. Orchestrator reads MEMORY_LOG.md (finds no prior context)
  3. Planning phase runs: Business Analyst + Architect produce a plan
  4. Plan saved to branch; Alice commits .github/knowledge/ updates

Day 2 — Bob (IntelliJ)
  5. Bob pulls latest from the branch
  6. Bob invokes @backend-engineer: "Implement [feature] per plan"
  7. Copilot in IntelliJ reads MEMORY_LOG.md (sees Alice's Day 1 decisions)
  8. Backend Engineer implements the feature in IntelliJ
  9. For test execution: Bob runs the project's test command in IntelliJ Terminal (Alt+F12)
 10. Bob pastes results into Copilot Chat; agent logs outcome to MEMORY_LOG.md
 11. Bob commits and pushes changes

Day 3 — Alice (VS Code)
 12. Alice pulls latest; sees Bob's implementation
 13. Alice invokes @qa-engineer: "Review and validate the new [feature]"
 14. QA Engineer reads MEMORY_LOG.md (sees full history from Days 1–2)
 15. Validation passes; QA Engineer logs to METRICS.md
 16. Alice commits .github/knowledge/ updates
```

**Result:** Full workflow completed across two IDEs with no manual coordination overhead.
IDE choice was transparent at every step.

---

## Agent Handoff Across IDEs

When the Orchestrator needs to delegate (handoff) to a specialist:

**In VS Code:** Copilot's `runSubagent` handles this automatically.

**In IntelliJ:** The engineer facilitates the handoff manually:

1. Note the handoff template from the Orchestrator's output.
2. Open a new Copilot Chat session.
3. Address the specialist agent with the full handoff context.
4. Complete the work.
5. Return to the Orchestrator session with the result summary.

See [`.github/integrations/fallbacks.md`](../integrations/fallbacks.md) for the full
manual handoff procedure.

---

## Team Conventions for Cross-IDE Work

### Commit discipline

- **Always commit `.github/knowledge/` files** after completing a task.
- Never leave memory artifacts uncommitted at end of day.
- Commit message format for knowledge-only updates: `docs(knowledge): [brief description]`

### Branch strategy

- Same branch strategy applies regardless of IDE.
- Knowledge artifacts are committed on the feature branch, not directly to main.
- When merging, merge conflicts in `.github/knowledge/` are resolved by concatenating entries.

### Communication

- If you discover an IDE-specific tool behavior difference, log it to `MEMORY_LOG.md`
  as type `ISSUE` and update `.github/capabilities/tool-mapping.md`.
- Do not patch around tool gaps silently — document them so the team can address them.

---

## What Changes When You Switch IDEs

| Aspect                          | Changes                        | Stays the Same |
| ------------------------------- | ------------------------------ | -------------- |
| Copilot chat UX                 | Yes (different sidebar layout) | —              |
| `@agent-name` invocation syntax | No                             | ✅             |
| Agent behavior and rules        | No                             | ✅             |
| 7-phase lifecycle               | No                             | ✅             |
| Memory artifacts                | No                             | ✅             |
| Governance and guardrails       | No                             | ✅             |
| Tool availability (some gaps)   | Yes                            | —              |
| Handoff mechanism               | Yes (manual in IntelliJ)       | —              |
| Project scripts and tests       | No                             | ✅             |
| Knowledge artifact format       | No                             | ✅             |

---

## Onboarding a New Engineer (Cross-IDE)

1. Clone the repository.
2. Read `.github/AGENTS.md` — agent roles, lifecycle, governance.
3. Read `.github/AI_WORKFLOW_GUIDE.md` — quickstart for any IDE.
4. Choose your IDE and read its integration guide:
   - VS Code: `.github/integrations/vscode.md`
   - IntelliJ: `.github/integrations/intellij.md`
5. Read `.github/knowledge/MEMORY_LOG.md` — understand recent team decisions.
6. Try a small task with `@orchestrator` to get familiar with the workflow.

---

## Escalation

If an IDE-specific issue blocks progress after one attempted fallback:

1. Log the issue to `MEMORY_LOG.md` as type `ISSUE`.
2. Switch to the other IDE for the blocked task if feasible.
3. Raise the issue in the next team discussion.
4. If it is a systematic gap (not a one-time glitch), create a ticket in the project backlog
   and reference the `MEMORY_LOG.md` entry.
