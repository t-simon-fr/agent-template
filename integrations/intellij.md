# IntelliJ Integration Guide

This guide covers how to use the AI-assisted development workflow in **IntelliJ-based IDEs**
(IntelliJ IDEA, PyCharm, WebStorm, etc.) with the **GitHub Copilot** plugin.

The agent definitions, governance rules, and knowledge artifacts are all in `.github/` and are
IDE-independent. This guide covers only the IntelliJ-specific setup and workflow patterns.

For the full agent and governance reference, see:

- Agent roles: [`.github/AGENTS.md`](../AGENTS.md)
- Capabilities: [`.github/capabilities/capabilities.md`](../capabilities/capabilities.md)
- Tool mappings: [`.github/capabilities/tool-mapping.md`](../capabilities/tool-mapping.md)

---

## Prerequisites

- IntelliJ IDEA (or other JetBrains IDE), latest stable or 2024.1+
- [GitHub Copilot plugin for JetBrains](https://plugins.jetbrains.com/plugin/17718-github-copilot)
- Any build tools, runtimes, and environment variables required by your project (see [`AGENTS.md`](../AGENTS.md) — Codebase Guide)

---

## Setup

1. Clone the repository and open it in IntelliJ.
2. Install the GitHub Copilot plugin: **Settings → Plugins → Marketplace → "GitHub Copilot"**.
3. Sign in to GitHub Copilot via the plugin.
4. Open Copilot Chat: **Tools → GitHub Copilot → Open Chat** (or use the sidebar icon).
5. The repository's `.github/` folder provides Copilot with agent context automatically
   when you reference agent files in the chat.

---

## How to Invoke Agents

The GitHub Copilot plugin for IntelliJ supports `@agent-name` mentions in chat,
the same as VS Code.

### Starting a workflow

Invoke the **Orchestrator** for any non-trivial task:

```
@orchestrator I need to [describe what you need to accomplish]
```

### Invoking specialist agents directly

```
@backend-engineer  Fix the failing test in [TestClass]
@qa-engineer       Write a test for the new [feature/function]
@debugger          Investigate why [script/test] fails with [error message]
@critic            Review the changes in [file or area]
```

### Manual agent handoff (workaround for missing `spawn_subagent`)

IntelliJ Copilot does not support programmatic subagent spawning.
When the Orchestrator needs to delegate to a specialist:

1. Note the **handoff template** provided by the Orchestrator (task description, inputs, acceptance criteria).
2. Open a **new Copilot Chat** session.
3. Address the specialist agent directly and paste the handoff context:
   ```
   @backend-engineer — Orchestrator handoff:
   Task: [task from handoff]
   Inputs: [relevant files/context]
   Acceptance criteria: [criteria]
   ```
4. Complete the specialist's work in that session.
5. Return the result to the Orchestrator's original session:
   ```
   @orchestrator — Handoff complete from Backend Engineer:
   What changed: [summary]
   Files modified: [list]
   Test results: [outcome]
   ```

This manual delegation ensures the 7-phase lifecycle is maintained even without
native multi-agent support in IntelliJ.

---

## Available Tools in IntelliJ

Most capabilities are available via the IntelliJ Copilot plugin. The following have
known gaps or require validation:

| Capability                 | IntelliJ Status | Notes                                             |
| -------------------------- | :-------------: | ------------------------------------------------- |
| `read_files`               |     ✅ Full     | `read_file` available                             |
| `search_code_text`         |     ✅ Full     | `grep_search` available                           |
| `search_code_semantic`     |     ✅ Full     | `semantic_search` available                       |
| `list_directory`           |     ✅ Full     | `list_dir` available                              |
| `implement_code_changes`   |     ✅ Full     | `create_file`, `replace_string_in_file` available |
| `run_terminal_commands`    |   ⚠️ Validate   | `run_in_terminal` — verify availability           |
| `get_terminal_output`      |   ⚠️ Validate   | Tied to `run_in_terminal`                         |
| `get_diagnostics`          |   ⚠️ Validate   | `get_errors` — may surface IDE inspections        |
| `manage_memory_artifacts`  |     ✅ Full     | Files in `.github/knowledge/`                     |
| `ask_clarifying_questions` |  ✅ Equivalent  | Natural-language chat (no structured UI)          |
| `spawn_subagent`           |   ❌ Missing    | Manual handoff required (see above)               |
| `search_web`               |   ❌ Missing    | Engineer provides content manually                |
| `view_image`               |   ❌ Missing    | Engineer describes image content                  |

For full gap details and mitigations, see [`tool-mapping.md`](../capabilities/tool-mapping.md).

---

## IntelliJ-Specific Workflow Patterns

### Using IntelliJ's built-in terminal for commands

When `run_in_terminal` is available via Copilot:

- Agents invoke it directly in the chat.

When it is **not** available (fallback):

1. Agent generates the exact command with all flags.
2. Open IntelliJ Terminal: **Alt+F12** (Windows/Linux) or **⌥F12** (macOS).
3. Run the command manually.
4. Paste the output back into Copilot Chat so the agent can analyze it:
   ```
   Here is the output of the command you requested:
   [paste output here]
   ```

### Using IntelliJ's structural search

For complex code search tasks (finding method signatures, usages, patterns):

- Use IntelliJ's **Edit → Find → Search Structurally** as a supplement to Copilot's `grep_search`.
- Describe search intent to Copilot; Copilot uses `grep_search` + `semantic_search`.

### Running project tests from IntelliJ

- Use your project's built-in run configuration or test runner tooling in IntelliJ.
- For suite-specific runs, use your project's configured tool window or terminal workflow.
- For Copilot-driven test runs, `run_in_terminal` (when available) invokes scripts directly.

### Getting diagnostics

When `get_errors` returns IntelliJ inspection results:

- These are IDE-level warnings, not always compiler errors.
- For authoritative diagnostics, run your project's build or compile command in terminal and paste output to Copilot.

---

## Memory Protocol

The memory and knowledge artifacts are in `.github/knowledge/` — plain Markdown files
committed to the repository. Both editors read and write the same files:

- `MEMORY_LOG.md` — Decisions, bugs, patterns, lessons
- `METRICS.md` — Task-level metrics
- `EVOLUTION_LOG.md` — Agent behavior improvements

IntelliJ Copilot can read and write these files the same way as VS Code Copilot.

**Optional**: When logging a memory entry, you may add your IDE as metadata:

```
| 2026-05-08 | backend-engineer | DECISION | (IntelliJ) Use async runner for test orchestration | Reduces blocking waits |
```

This is optional and for informational purposes only. IDE choice never changes the workflow.

---

## IDE Switching

If you switch to VS Code, all of the following remain identical:

- Agent behavior, roles, and governance (`.github/AGENTS.md`)
- Memory artifacts (`.github/knowledge/`)
- Capability definitions (`.github/capabilities/`)
- Repository structure and test scripts

Only the tool UX changes. See [`.github/integrations/vscode.md`](vscode.md).

---

## Troubleshooting

| Problem                          | Resolution                                                                                                                       |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Copilot chat not showing agents  | Ensure `.github/agents/` folder is in the workspace; reference the agent file in chat context                                    |
| `run_in_terminal` not available  | Use fallback: generate command, run in Alt+F12 terminal, paste output                                                            |
| Agent does not follow governance | Explicitly reference `Read .github/AGENTS.md` at the start of the chat                                                           |
| Memory log not being updated     | Explicitly prompt: "Append an entry to `.github/knowledge/MEMORY_LOG.md`"                                                        |
| Multi-agent delegation fails     | Use manual handoff pattern (see "Manual agent handoff" section above)                                                            |
| Build fails                      | Check your project's build tool window or terminal output; verify required environment variables per [`AGENTS.md`](../AGENTS.md) |
| Runtime or SDK mismatch          | Verify project SDK/runtime settings in IntelliJ and confirm requirements in [`AGENTS.md`](../AGENTS.md)                          |

---

## Reporting Issues

If IntelliJ Copilot behaves differently from this guide or from VS Code Copilot,
log it to `.github/knowledge/MEMORY_LOG.md` as type `ISSUE`:

```
| [date] | [Your-Agent] | ISSUE | IntelliJ: [describe behavior difference] | Workaround: [what you did] |
```

This ensures the team tracks tool gaps and maintains accurate capability mappings.
