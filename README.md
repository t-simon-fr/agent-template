# Project Template — Self-Improving Multi-Agent System v2

Starter template with a self-improving multi-agent Copilot workflow featuring a 7-phase lifecycle, persistent memory, observability, and agent evolution capabilities.

## Architecture

### 7-Phase Lifecycle

```
Plan → Build → Test → Critique → Refactor → Re-test → Learn
```

Each iteration follows this loop. Maximum 3 iterations before user escalation.

### Agents (10)

| Agent                 | Role                                                              | Tools                             |
| --------------------- | ----------------------------------------------------------------- | --------------------------------- |
| **orchestrator**      | Coordinates specialists, enforces lifecycle, integrates delivery  | agent, todo, read, search         |
| **product-manager**   | Validates user value, prioritizes by impact                       | read, search, web, todo           |
| **business-analyst**  | Clarifies requirements, defines acceptance criteria               | read, search, web, todo           |
| **architect**         | Designs technical blueprint, defines contracts, threat modeling   | read, search, web, todo           |
| **frontend-engineer** | Implements UI, state flows, accessibility                         | read, edit, search, execute, todo |
| **backend-engineer**  | Implements APIs, data access, security hardening, OWASP checklist | read, edit, search, execute, todo |
| **qa-engineer**       | Tests, generates test cases, detects regressions                  | read, edit, search, execute, todo |
| **debugger**          | Root-cause analysis, performance profiling                        | read, search, execute, todo       |
| **user-proxy**        | Reviews outputs for usability and accessibility                   | read, search, todo                |
| **critic**            | Reviews quality, drives self-improvement loop                     | read, search, todo                |

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
    product-manager.agent.md       # Product intelligence, backlog owner
    business-analyst.agent.md      # Requirements analyst
    architect.agent.md             # Solutions architect
    frontend-engineer.agent.md     # UI implementation
    backend-engineer.agent.md      # API implementation
    qa-engineer.agent.md           # Autonomous QA
    debugger.agent.md              # Root-cause diagnostics
    user-proxy.agent.md            # End-user representative
    critic.agent.md                # Quality & self-improvement
  instructions/
    guardrails.instructions.md     # System-wide guardrails
  knowledge/
    MEMORY_LOG.md                  # Persistent agent memory
    METRICS.md                     # Observability metrics
    EVOLUTION_LOG.md               # Agent evolution tracking
docs/
  PROJECT_MISSION.md               # Project goals and success criteria
  DELIVERY_PLAN.md                 # Current cycle delivery plan
  BACKLOG.md                       # Product backlog
```

## Documentation Model

Three documentation tiers keep this template reusable while giving each project clean local context.

### Tier 1: Reusable Agent System (Generic)

- `.github/AGENTS.md` — Shared governance policy
- `.github/agents/*.agent.md` — Agent role definitions
- `.github/instructions/*` — System-wide instructions

These define reusable operating policy and role behavior. Improvements here propagate to all future projects.

### Tier 2: Project Documentation (`docs/`)

- `docs/PROJECT_MISSION.md` — Project goals, scope, and success criteria
- `docs/DELIVERY_PLAN.md` — Current cycle plan with acceptance criteria
- `docs/BACKLOG.md` — Product backlog managed by Product Manager

These are initialized per project and contain human-facing planning documents. The backlog drives the SDLC: items flow from backlog → delivery plan → 7-phase execution.

### Tier 3: Agent Operational Knowledge (`.github/knowledge/`)

- `.github/knowledge/MEMORY_LOG.md` — Decisions, bugs, patterns, lessons
- `.github/knowledge/METRICS.md` — Task-level observability metrics
- `.github/knowledge/EVOLUTION_LOG.md` — Agent behavior change tracking

These accumulate during project execution and are read/written by agents automatically.

## New Project Initialization

1. Copy this template repository to your new project location.
2. Open `docs/PROJECT_MISSION.md` and replace all placeholder fields with your project goals.
3. Open `docs/DELIVERY_PLAN.md` and fill the initial scope, acceptance criteria, and execution owners.
4. Review `docs/BACKLOG.md` — add initial backlog items with priorities (P0/P1/P2).
5. Keep bootstrap rows in `MEMORY_LOG.md`, `METRICS.md`, and `EVOLUTION_LOG.md`, then append project-specific entries during execution.
6. Run your first orchestrated cycle with the `orchestrator` agent mode and verify new log rows are appended.

## Cross-Project Improvement Loop

- When a process or policy improvement proves useful in one project, update the generic docs in the template (`AGENTS.md`, agent files, instructions) so future projects inherit it.
- Keep project-specific learnings in that project's `docs/` and `.github/knowledge/` files unless the pattern generalizes.
- Optionally mirror stable generic improvements to user-level prompts/instructions in your VS Code user prompt folder for cross-workspace reuse.

## Getting Started

1. Fill in `docs/PROJECT_MISSION.md` with your project goals.
2. Fill in `docs/DELIVERY_PLAN.md` with your delivery plan.
3. Add initial work items to `docs/BACKLOG.md`.
4. Use the `orchestrator` agent mode in Copilot Chat to coordinate work.
5. The system will automatically:
   - Read the backlog and plan with product-manager, business-analyst, architect, and user-proxy (early UX review)
   - Build with frontend/backend engineers
   - Test with qa-engineer (auto-generates test cases)
   - Critique with critic and user-proxy
   - Refactor based on critic feedback
   - Re-test to validate fixes
   - Learn, log to memory, and update backlog status
6. Review `MEMORY_LOG.md`, `METRICS.md`, and `EVOLUTION_LOG.md` periodically.

## Guardrails

- **3 iteration maximum** — Escalates to user if issues persist
- **Scope creep detection** — New requirements go to backlog
- **Overengineering guard** — Simplest solution that meets criteria
- **Self-modification ban** — No agent modifies its own instructions
- **Security** — OWASP Top 10 enforced, no hardcoded secrets
