# .github — AI Agent Team Template

This folder is a **ready-to-use template** for setting up a self-improving, multi-agent AI development workflow powered by GitHub Copilot. Copy it into any repository to instantly give your team a structured agentic workflow with 10 specialized roles, a 7-phase lifecycle, shared memory, and cross-IDE support (VS Code + IntelliJ).

---

## Getting Started

Follow these steps to adopt this template in your repository.

### Step 1 — Copy this folder into your repository

Copy the entire `.github/` folder into the root of your repository. If your repository already has a `.github/` folder, merge the contents.

```
your-repo/
└─ .github/        ← copy everything here
```

### Step 2 — Fill in the Codebase Guide in AGENTS.md

Open [`AGENTS.md`](AGENTS.md) and scroll to the **Codebase Guide** section at the bottom. Fill in every `[placeholder]` with details about your project:

- Architecture overview and key modules
- Required environment variables and tools
- Build and test commands
- Runner scripts and where they live
- Data/resource layout
- Platform-specific notes and common pitfalls

This section is the single source of truth agents use to understand your codebase.

### Step 3 — Update the workflow guide

Open [`AI_WORKFLOW_GUIDE.md`](AI_WORKFLOW_GUIDE.md) and:

- Replace `[your-repository-name]` with your actual repo name
- Fill in the **Test Stack** table with your real test suites and runners

### Step 4 — Set up your IDE

| IDE      | Setup guide                                          |
| -------- | ---------------------------------------------------- |
| VS Code  | [integrations/vscode.md](integrations/vscode.md)     |
| IntelliJ | [integrations/intellij.md](integrations/intellij.md) |

Both guides explain how to activate the agent definitions and configure GitHub Copilot to use this workflow.

### Step 5 — Clear the knowledge logs (optional but recommended)

The `knowledge/` files ship with bootstrap entries from the template. Before your first real work session, you can trim them to a clean slate or leave the bootstrap entries as context for agents.

- [`knowledge/MEMORY_LOG.md`](knowledge/MEMORY_LOG.md) — remove or keep bootstrap entries
- [`knowledge/METRICS.md`](knowledge/METRICS.md) — remove or keep bootstrap entries
- [`knowledge/EVOLUTION_LOG.md`](knowledge/EVOLUTION_LOG.md) — remove or keep bootstrap entries

### Step 6 — Start using the agents

Invoke the Orchestrator for any multi-step task:

```
@orchestrator I need to [describe what you need to accomplish]
```

See [`AI_WORKFLOW_GUIDE.md`](AI_WORKFLOW_GUIDE.md) for the full agent roster and common task examples.

---

## What's Included

This template provides a complete agentic workflow out of the box:

| Component                 | What it does                                                                         |
| ------------------------- | ------------------------------------------------------------------------------------ |
| **10 specialized agents** | Orchestrator, PM, BA, Architect, Frontend, Backend, QA, Debugger, Critic, User Proxy |
| **7-phase lifecycle**     | Planning → Executing → Testing → Critique → Refactoring → Re-testing → Learning      |
| **Shared memory**         | Persistent logs for decisions, bugs, patterns, metrics, and evolution                |
| **Cross-IDE support**     | Works identically in VS Code and IntelliJ via a capability abstraction layer         |
| **Quality guardrails**    | Iteration limits, scope discipline, security rules, phase-skip governance            |
| **Fast-track mode**       | Streamlined flow for low-risk, well-understood changes                               |

---

## Quick Navigation

| I want to...                                 | Go to                                                                                                    |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Understand the AI workflow                   | [AI_WORKFLOW_GUIDE.md](AI_WORKFLOW_GUIDE.md)                                                             |
| Read agent roles and governance              | [AGENTS.md](AGENTS.md)                                                                                   |
| See recent team decisions                    | [knowledge/MEMORY_LOG.md](knowledge/MEMORY_LOG.md)                                                       |
| Set up my IDE                                | [integrations/vscode.md](integrations/vscode.md) or [integrations/intellij.md](integrations/intellij.md) |
| Understand what agents can do                | [capabilities/capabilities.md](capabilities/capabilities.md)                                             |
| See which tools map to which IDE             | [capabilities/tool-mapping.md](capabilities/tool-mapping.md)                                             |
| Coordinate with teammates on a different IDE | [standards/cross-ide-collaboration.md](standards/cross-ide-collaboration.md)                             |
| Handle a tool that's not available           | [integrations/fallbacks.md](integrations/fallbacks.md)                                                   |

---

## Folder Structure

```
.github/
│
├─ AGENTS.md                              # Agent governance, roles, lifecycle, global rules
├─ AI_WORKFLOW_GUIDE.md                   # High-level quickstart guide for any IDE
├─ README.md                              # This file
│
├─ agents/                                # Specialized agent role definitions
│   ├─ orchestrator.agent.md              # Lifecycle management, delegation
│   ├─ product-manager.agent.md           # Value validation, prioritization
│   ├─ business-analyst.agent.md          # Requirements, acceptance criteria
│   ├─ architect.agent.md                 # System design, trade-off analysis
│   ├─ frontend-engineer.agent.md         # UI implementation
│   ├─ backend-engineer.agent.md          # API/domain implementation
│   ├─ qa-engineer.agent.md               # Test design, test generation, validation
│   ├─ debugger.agent.md                  # Root-cause analysis
│   ├─ critic.agent.md                    # Quality review, self-improvement
│   └─ user-proxy.agent.md                # End-user perspective
│
├─ capabilities/                          # Abstract capability model (IDE-independent)
│   ├─ capabilities.md                    # What agents can do (abstract definitions)
│   ├─ tool-mapping.md                    # Capability → tool mapping per IDE
│   └─ profiles.md                        # Per-agent capability requirements
│
├─ integrations/                          # Editor-specific setup and workflow guides
│   ├─ vscode.md                          # VS Code + GitHub Copilot setup
│   ├─ intellij.md                        # IntelliJ + GitHub Copilot setup
│   └─ fallbacks.md                       # Graceful degradation when tools are unavailable
│
├─ instructions/                          # Workflow standards applied automatically
│   └─ copilot.instructions.md            # Workflow guardrails: iteration limits, scope discipline, quality gates
│
├─ knowledge/                             # Persistent shared team knowledge
│   ├─ MEMORY_LOG.md                      # Decisions, bugs, patterns, lessons
│   ├─ METRICS.md                         # Task completion metrics and quality scores
│   └─ EVOLUTION_LOG.md                   # Agent behavior improvements and approvals
│
└─ standards/                             # Team collaboration standards
    └─ cross-ide-collaboration.md         # How mixed-IDE teams coordinate
```

---

## Architecture at a Glance

```
Repository (.github/)  ←── Source of truth for ALL shared AI behavior
│
├─ Governance & Roles (AGENTS.md + agents/)
├─ Capabilities (capabilities/)            ← What agents can do, abstractly
├─ Knowledge (knowledge/)                  ← Shared team memory, metrics, evolution
├─ Standards (instructions/, standards/)   ← Rules that apply to all agents, all IDEs
│
└─ IDE Integration (integrations/)         ← Thin layer: how each editor fulfills capabilities
    │
    ├─ vscode.md      → VS Code + Copilot
    └─ intellij.md    → IntelliJ + Copilot
```

The capability abstraction layer ensures that agent definitions do not depend on
any specific editor or tool. When GitHub Copilot or an IDE updates its tools,
only `tool-mapping.md` needs to change — not the agent definitions.

---

## Maintenance

| Task                          | Who                     | Where                                  |
| ----------------------------- | ----------------------- | -------------------------------------- |
| Add or update an agent role   | Orchestrator (approval) | `agents/` + `AGENTS.md`                |
| Update capability definitions | Orchestrator (approval) | `capabilities/capabilities.md`         |
| Update tool mappings          | Orchestrator (approval) | `capabilities/tool-mapping.md`         |
| Add IDE support               | Orchestrator (approval) | `integrations/` + `tool-mapping.md`    |
| Log decisions or issues       | All agents (required)   | `knowledge/MEMORY_LOG.md`              |
| Log task metrics              | All agents (required)   | `knowledge/METRICS.md`                 |
| Propose agent improvements    | Critic                  | `knowledge/EVOLUTION_LOG.md`           |
| Update guardrails             | Orchestrator (approval) | `instructions/copilot.instructions.md` |

All structural changes to this folder require Orchestrator approval and an entry
in `knowledge/EVOLUTION_LOG.md`.
