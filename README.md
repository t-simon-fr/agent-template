# Project Template — Self-Improving Multi-Agent System v2

Starter template with a self-improving multi-agent Copilot workflow featuring a 7-phase lifecycle, persistent memory, observability, and agent evolution capabilities.

## Architecture

### 7-Phase Lifecycle

```
Plan → Build → Test → Critique → Refactor → Re-test → Learn
```

Each iteration follows this loop. Maximum 3 iterations before user escalation.

### Agents (10)

| Agent                 | Role                                                             | Tools                             |
| --------------------- | ---------------------------------------------------------------- | --------------------------------- |
| **orchestrator**      | Coordinates specialists, enforces lifecycle, integrates delivery | agent, todo, read, search         |
| **product-manager**   | Validates user value, prioritizes by impact                      | read, search, web, todo           |
| **business-analyst**  | Clarifies requirements, defines acceptance criteria              | read, search, web, todo           |
| **architect**         | Designs technical blueprint, defines contracts                   | read, search, web, todo           |
| **frontend-engineer** | Implements UI, state flows, accessibility                        | read, edit, search, execute, todo |
| **backend-engineer**  | Implements APIs, data access, security                           | read, edit, search, execute, todo |
| **qa-engineer**       | Tests, generates test cases, detects regressions                 | read, edit, search, execute, todo |
| **debugger**          | Root-cause analysis, performance profiling                       | read, search, execute, todo       |
| **user-proxy**        | Reviews outputs for usability and accessibility                  | read, search, todo                |
| **critic**            | Reviews quality, drives self-improvement loop                    | read, search, todo                |

### Self-Improving Capabilities

- **Persistent Memory** — All agents read/write to `MEMORY_LOG.md` (decisions, bugs, patterns, lessons)
- **Observability** — Task metrics tracked in `METRICS.md` (success rate, quality scores)
- **Agent Evolution** — Critic proposes agent improvements in `EVOLUTION_LOG.md`
- **Feedback Loop** — Critique → Refactor → Re-test cycle with max 3 iterations

## Structure

```
.github/
  AGENTS.md                        # Shared policy (v2 — 7-phase lifecycle)
  agents/
    orchestrator.agent.md          # Lead coordinator
    product-manager.agent.md       # Product intelligence (NEW)
    business-analyst.agent.md      # Requirements analyst
    architect.agent.md             # Solutions architect
    frontend-engineer.agent.md     # UI implementation
    backend-engineer.agent.md      # API implementation
    qa-engineer.agent.md           # Autonomous QA (UPGRADED)
    debugger.agent.md              # Root-cause diagnostics
    user-proxy.agent.md            # End-user representative (NEW)
    critic.agent.md                # Quality & self-improvement (NEW)
  instructions/
    guardrails.instructions.md     # System-wide guardrails (NEW)
  knowledge/
    PROJECT_MISSION.md             # Fill in your project goals
    DELIVERY_PLAN.md               # Fill in your delivery plan
    MEMORY_LOG.md                  # Persistent agent memory (NEW)
    METRICS.md                     # Observability metrics (NEW)
    EVOLUTION_LOG.md               # Agent evolution tracking (NEW)
```

## Documentation Model

Use two documentation classes so this template stays reusable while each project starts with clean context.

### Reusable Across Projects (Generic)

- `.github/AGENTS.md`
- `.github/agents/*.agent.md`
- `.github/instructions/*`

These define reusable operating policy and role behavior that should improve across projects.

### Project-Initialized (Per Project)

- `.github/knowledge/PROJECT_MISSION.md`
- `.github/knowledge/DELIVERY_PLAN.md`
- `.github/knowledge/MEMORY_LOG.md`
- `.github/knowledge/METRICS.md`
- `.github/knowledge/EVOLUTION_LOG.md`

These should be initialized for each new project and then treated as project-local history.

## New Project Initialization

1. Copy this template repository to your new project location.
2. Open `.github/knowledge/PROJECT_MISSION.md` and replace all placeholder fields.
3. Open `.github/knowledge/DELIVERY_PLAN.md` and fill the initial in-scope rows, acceptance criteria, and execution owners.
4. Keep bootstrap rows in `MEMORY_LOG.md`, `METRICS.md`, and `EVOLUTION_LOG.md`, then append project-specific entries only.
5. Run your first orchestrated cycle with the `orchestrator` agent mode and verify new log rows are appended.

## Cross-Project Improvement Loop

- When a process or policy improvement proves useful in one project, update the generic docs in the template (`AGENTS.md`, agent files, instructions) so future projects inherit it.
- Keep project-specific learnings in that project's `.github/knowledge/` files unless the pattern generalizes.
- Optionally mirror stable generic improvements to user-level prompts/instructions in your VS Code user prompt folder for cross-workspace reuse.

## Getting Started

1. Fill in `.github/knowledge/PROJECT_MISSION.md` with your project goals.
2. Fill in `.github/knowledge/DELIVERY_PLAN.md` with your delivery plan.
3. Use the `orchestrator` agent mode in Copilot Chat to coordinate work.
4. The system will automatically:
   - Plan with product-manager, business-analyst, and architect
   - Build with frontend/backend engineers
   - Test with qa-engineer (auto-generates test cases)
   - Critique with critic and user-proxy
   - Refactor based on critic feedback
   - Re-test to validate fixes
   - Learn and log to memory for future improvement
5. Review `MEMORY_LOG.md`, `METRICS.md`, and `EVOLUTION_LOG.md` periodically.

## Guardrails

- **3 iteration maximum** — Escalates to user if issues persist
- **Scope creep detection** — New requirements go to backlog
- **Overengineering guard** — Simplest solution that meets criteria
- **Self-modification ban** — No agent modifies its own instructions
- **Security** — OWASP Top 10 enforced, no hardcoded secrets
