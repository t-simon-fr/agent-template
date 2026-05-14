# VS Code Integration Guide

This guide covers how to use the AI-assisted development workflow in **Visual Studio Code**
with the **GitHub Copilot** extension.

The agent definitions, governance rules, and knowledge artifacts are all in `.github/` and are
IDE-independent. This guide covers only the VS Code-specific setup and workflow patterns.

For the full agent and governance reference, see:

- Agent roles: [`.github/AGENTS.md`](../AGENTS.md)
- Capabilities: [`.github/capabilities/capabilities.md`](../capabilities/capabilities.md)
- Tool mappings: [`.github/capabilities/tool-mapping.md`](../capabilities/tool-mapping.md)

---

## Prerequisites

- Visual Studio Code (latest stable)
- [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [GitHub Copilot Chat extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)
- Any build tools, runtimes, and environment variables required by your project (see [`AGENTS.md`](../AGENTS.md) — Codebase Guide)

---

## Setup

1. Clone the repository and open it in VS Code.
2. Install the recommended extensions (`.vscode/extensions.json` if present).
3. Open GitHub Copilot Chat (`Ctrl+Alt+I` or click the Copilot icon in the sidebar).
4. The repository's `.github/` folder provides Copilot with agent context automatically.

---

## How to Invoke Agents

### Starting a workflow

Invoke the **Orchestrator** for any non-trivial task:

```
@orchestrator I need to [describe what you need to accomplish]
```

The Orchestrator will:

1. Read `MEMORY_LOG.md` for relevant prior context.
2. Run Planning phases with appropriate specialist agents.
3. Delegate implementation to Backend Engineer or QA Engineer.
4. Coordinate Testing, Critique, and Learning phases.

### Invoking specialist agents directly

For well-scoped tasks where the phase is clear:

```
@backend-engineer  Fix the failing test in [TestClass] that expects [behavior]
@qa-engineer       Write a test for the new [feature/function]
@debugger          Investigate why [script/test] fails with [error message]
@critic            Review the changes in [file or area] for quality and test coverage
```

### Fast-track (low-risk tasks)

For simple, well-understood changes, bypass full Planning and go directly to implementation.
Log the phase skip in `MEMORY_LOG.md` as required by governance.

---

## Available Tools in VS Code

VS Code + Copilot provides the full capability set. All capabilities in
`capabilities.md` are available. Highlights:

| Capability                 | VS Code Tool(s)                                                         |
| -------------------------- | ----------------------------------------------------------------------- |
| `read_files`               | `read_file`, `fetch_webpage`                                            |
| `search_code_text`         | `grep_search`                                                           |
| `search_code_semantic`     | `semantic_search`                                                       |
| `list_directory`           | `list_dir`                                                              |
| `implement_code_changes`   | `create_file`, `replace_string_in_file`, `multi_replace_string_in_file` |
| `run_terminal_commands`    | `run_in_terminal`                                                       |
| `get_terminal_output`      | `get_terminal_output`                                                   |
| `get_diagnostics`          | `get_errors`                                                            |
| `spawn_subagent`           | `runSubagent`                                                           |
| `ask_clarifying_questions` | `vscode_askQuestions`                                                   |

---

## Workflow Patterns

### Memory protocol before starting

Any non-trivial task must begin with reading the memory log:

```
Read .github/knowledge/MEMORY_LOG.md and summarize any prior context relevant to [area of work].
```

### After completing work

Log outcomes:

```
Append an entry to .github/knowledge/MEMORY_LOG.md:
- Type: DECISION / OUTCOME / LESSON (choose appropriate)
- Description: [what was done or decided]
- Resolution: [result or outcome]

Also append a row to .github/knowledge/METRICS.md for this task.
```

### Running tests

Agents will run your project's test commands as defined in the **Codebase Guide** section of [`AGENTS.md`](../AGENTS.md). Fill in that section with your actual build and runner commands.

---

## IDE Switching

If you switch to IntelliJ, all of the following remain identical:

- Agent behavior, roles, and governance (`.github/AGENTS.md`)
- Memory artifacts (`.github/knowledge/`)
- Capability definitions (`.github/capabilities/`)
- Repository structure and test scripts

Only the tool invocation UX changes. See [`.github/integrations/intellij.md`](intellij.md).

---

## Known Limitations

- `vscode_askQuestions` (structured question prompts) is VS Code-specific. IntelliJ uses chat-based questions instead.
- `fetch_webpage` (web content retrieval) is VS Code-specific. Use manual lookup in IntelliJ.
- `runSubagent` (multi-agent delegation) is VS Code-specific. IntelliJ requires manual agent switching.

These gaps are documented in [`tool-mapping.md`](../capabilities/tool-mapping.md).

---

## Troubleshooting

| Problem                                    | Resolution                                                                                       |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| Agent does not follow AGENTS.md governance | Open `.github/instructions/copilot.instructions.md` and verify it references `.github/AGENTS.md` |
| Memory log not being read before tasks     | Explicitly prompt: "Read MEMORY_LOG.md first, then…"                                             |
| Test runner fails with environment error   | Check required environment variables defined in [`AGENTS.md`](../AGENTS.md) — Codebase Guide     |
| Build fails                                | Consult project's build documentation in [`AGENTS.md`](../AGENTS.md) — Build & Test Workflows    |
| `run_in_terminal` returns no output        | Use `get_terminal_output` to retrieve the result of the previous command                         |
