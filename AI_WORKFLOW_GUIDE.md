# AI Workflow Guide

Welcome to the AI-assisted development workflow for the **[your-repository-name]** repository.
This guide gets you productive quickly, regardless of whether you use VS Code or IntelliJ.

---

## What Is This Workflow?

This repository uses a **multi-agent AI system** powered by GitHub Copilot.
Instead of working directly with a general-purpose assistant, you work with
**specialized agents** — each with a defined role, scope, and set of capabilities.

The workflow is governed by:

- **Agent roles** — 10 specialized agents, each with a specific purpose.
- **7-phase lifecycle** — Planning → Executing → Testing → Critique → Refactoring → Re-testing → Learning.
- **Shared memory** — A team-wide knowledge base in `.github/knowledge/`.
- **Governance rules** — Standards that apply to all agents and all IDEs.

---

## Quick Start (5 Minutes)

### Step 1 — Choose your IDE and read its integration guide

| IDE      | Integration Guide                                      |
| -------- | ------------------------------------------------------ |
| VS Code  | [`integrations/vscode.md`](integrations/vscode.md)     |
| IntelliJ | [`integrations/intellij.md`](integrations/intellij.md) |

### Step 2 — Read the recent memory log

Before starting any non-trivial task, read the team's shared memory:

```
Read .github/knowledge/MEMORY_LOG.md and summarize the most recent 10 entries.
```

### Step 3 — Start with the Orchestrator

For any task that involves more than one file or phase:

```
@orchestrator I need to [describe what you need to accomplish]
```

The Orchestrator handles phase management, delegation, and final delivery.

---

## Agent Roles (Summary)

| Agent                | Invoke When You Need To...                                      |
| -------------------- | --------------------------------------------------------------- |
| `@orchestrator`      | Start any multi-step task; manage phases; coordinate agents     |
| `@product-manager`   | Validate user value; prioritize work; define outcomes           |
| `@business-analyst`  | Clarify requirements; write acceptance criteria; define scope   |
| `@architect`         | Design system structure; evaluate technical trade-offs          |
| `@frontend-engineer` | Implement UI; build state flows; fix accessibility issues       |
| `@backend-engineer`  | Implement APIs; fix backend logic; security hardening           |
| `@qa-engineer`       | Write or run tests; validate behavior; report defects           |
| `@debugger`          | Diagnose errors; analyze stack traces; investigate failures     |
| `@critic`            | Review code quality; audit test coverage; evaluate improvements |
| `@user-proxy`        | Check UX clarity; validate usability; raise friction points     |

Full role definitions: [`AGENTS.md`](AGENTS.md)

---

## Common Tasks

### Implement a new feature

```
@orchestrator Plan and implement [feature description].
              Acceptance criteria: [describe expected behavior].
```

The Orchestrator runs Planning phases, then delegates to Backend or Frontend Engineer.

### Fix a failing test

```
@debugger     Investigate why [test name] is failing.
              Error: [paste error message or stack trace]
```

### Write tests for new behavior

```
@qa-engineer  Write tests for [feature/function].
              The behavior should: [describe expected behavior].
```

### Review a change for quality

```
@critic       Review the changes in [file or area].
              Focus on: [quality / test coverage / security / all].
```

### Understand the codebase

```
@architect    Explain the architecture of [component].
              How does [feature] work end-to-end?
```

---

## The 7-Phase Lifecycle

```
Planning  ──→  Executing  ──→  Testing  ──→  Critique
   ↑                                             │
   │                                             ↓
Learning  ←──  Re-testing  ←──  Refactoring  ←──┘
```

| Phase           | Owner                              | Purpose                                                              |
| --------------- | ---------------------------------- | -------------------------------------------------------------------- |
| **Planning**    | Orchestrator + PM + BA + Architect | Understand requirements; design approach; define acceptance criteria |
| **Executing**   | Backend/Frontend Engineer          | Implement changes with validation and tests                          |
| **Testing**     | QA Engineer                        | Validate behavior; run regression checks; report defects             |
| **Critique**    | Critic + User Proxy                | Review quality, coverage, and usability                              |
| **Refactoring** | Backend/Frontend Engineer          | Apply only Critic-approved improvements                              |
| **Re-testing**  | QA Engineer                        | Confirm refactoring did not introduce regressions                    |
| **Learning**    | Orchestrator + Critic              | Log decisions, patterns, lessons to memory                           |

**Maximum 3 iterations.** If unresolved after 3 full cycles, the Orchestrator escalates to you.

---

## Memory & Knowledge Artifacts

These three files are the team's shared knowledge base. All agents read and write them.

| File                                             | Purpose                                    | Read When                                   |
| ------------------------------------------------ | ------------------------------------------ | ------------------------------------------- |
| [`MEMORY_LOG.md`](knowledge/MEMORY_LOG.md)       | Decisions, bugs, patterns, lessons         | Before any non-trivial task                 |
| [`METRICS.md`](knowledge/METRICS.md)             | Task completion metrics and quality scores | When reviewing throughput or quality trends |
| [`EVOLUTION_LOG.md`](knowledge/EVOLUTION_LOG.md) | Agent behavior improvements and approvals  | When proposing changes to agent definitions |

**Always commit these files** after a work session so the team sees your updates.

---

## Governance at a Glance

- **No agent modifies its own `.agent.md` file.**
- **All external input must be validated.** Never hardcode secrets or credentials.
- **Scope discipline:** New requirements discovered mid-cycle go to backlog, not the current cycle.
- **Max 3 iterations:** If stuck, escalate to the human engineer.
- **Phase skips require Orchestrator approval** and a `MEMORY_LOG.md` entry.

Full governance: [`AGENTS.md`](AGENTS.md) and [`instructions/copilot.instructions.md`](instructions/copilot.instructions.md)

---

## This Repository's Test Stack

> **Fill in when adopting this template.** Replace the placeholder rows with your project's actual test suites and runners.

Quick reference for test execution:

| Suite        | Language / Framework | Runner             |
| ------------ | -------------------- | ------------------ |
| [Suite name] | [Language/Framework] | `[path/to/runner]` |
| [Suite name] | [Language/Framework] | `[path/to/runner]` |

Full details: [`AGENTS.md`](AGENTS.md) — Codebase Guide section.

---

## IDE Choice

Your IDE is your choice. All workflow behavior is identical in VS Code and IntelliJ.

The only differences are in the UX for invoking tools (e.g., IntelliJ requires manual
agent handoffs where VS Code supports automatic delegation). See:

- [`integrations/vscode.md`](integrations/vscode.md)
- [`integrations/intellij.md`](integrations/intellij.md)
- [`standards/cross-ide-collaboration.md`](standards/cross-ide-collaboration.md)

---

## Getting More Help

| Topic                                 | Document                                                                       |
| ------------------------------------- | ------------------------------------------------------------------------------ |
| Full agent role definitions           | [`AGENTS.md`](AGENTS.md)                                                       |
| Workflow guardrails                   | [`instructions/copilot.instructions.md`](instructions/copilot.instructions.md) |
| Capability model (what agents can do) | [`capabilities/capabilities.md`](capabilities/capabilities.md)                 |
| Tool mappings per IDE                 | [`capabilities/tool-mapping.md`](capabilities/tool-mapping.md)                 |
| Agent capability profiles             | [`capabilities/profiles.md`](capabilities/profiles.md)                         |
| VS Code setup                         | [`integrations/vscode.md`](integrations/vscode.md)                             |
| IntelliJ setup                        | [`integrations/intellij.md`](integrations/intellij.md)                         |
| Fallback strategies                   | [`integrations/fallbacks.md`](integrations/fallbacks.md)                       |
| Cross-IDE collaboration               | [`standards/cross-ide-collaboration.md`](standards/cross-ide-collaboration.md) |
| Team memory log                       | [`knowledge/MEMORY_LOG.md`](knowledge/MEMORY_LOG.md)                           |
