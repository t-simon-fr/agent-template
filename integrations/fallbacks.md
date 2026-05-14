# Fallback Strategies

This document defines how agents should behave when a specific tool or capability
is not available in the current IDE or environment.

Fallback behavior ensures workflows remain productive and consistent regardless
of IDE-specific tool availability.

---

## Fallback Protocol

When a required capability is unavailable:

1. **Check this document** for the documented fallback strategy.
2. **Use the fallback** to continue the task.
3. **Log usage** in `.github/knowledge/MEMORY_LOG.md` as type `PATTERN`:
   ```
   | [date] | [Agent] | PATTERN | Used fallback for [capability] in [IDE] | [brief outcome] |
   ```
4. If the fallback is used **more than twice in a week**, raise it in the next team
   discussion as a potential tool gap to address.

---

## Fallbacks by Capability

### `run_terminal_commands`

**When unavailable in:** IntelliJ (if `run_in_terminal` is not exposed by the Copilot plugin)

**Fallback:**

1. Agent generates the exact command as a code block with all flags and arguments.
2. Engineer opens IntelliJ Terminal (**Alt+F12** / **⌥F12**) or an external terminal.
3. Engineer runs the command manually.
4. Engineer pastes the complete output back into Copilot Chat:
   ```
   Here is the output:
   [paste output]
   ```
5. Agent analyzes the output and continues.

**Impact:** Minor workflow interruption; task completion time increases slightly.

---

### `get_terminal_output`

**When unavailable in:** IntelliJ (tied to `run_in_terminal`)

**Fallback:**

- Same as `run_terminal_commands` fallback above. Engineer pastes output into chat.

---

### `get_diagnostics`

**When IntelliJ `get_errors` surfaces IDE inspections instead of compiler errors:**

**Fallback:**

1. Agent runs the appropriate build/compile command for your project via `run_terminal_commands` (or its fallback). Consult the **Build & Test Workflows** section of [`AGENTS.md`](../AGENTS.md) for the correct command.
2. Engineer pastes the compiler output into Copilot Chat.
3. Agent analyzes actual compiler errors.

**Impact:** Requires one extra step; results are authoritative (compiler vs. IDE inspections).

---

### `spawn_subagent`

**When unavailable in:** IntelliJ (multi-agent delegation not supported by JetBrains Copilot plugin as of 2026)

**Fallback — Manual Handoff:**

1. **Orchestrator** prepares a handoff using the standard template from `AGENTS.md`:
   - Task description
   - Relevant files and context
   - Acceptance criteria
   - Phase and iteration number

2. **Engineer** opens a new Copilot Chat session and addresses the target agent:

   ```
   @[agent-name] — Orchestrator handoff (Iteration [N]):
   Phase: [Executing / Testing / Critique / ...]
   Task: [description]
   Context files: [list]
   Acceptance criteria: [criteria]
   Prior MEMORY_LOG context: [paste relevant entries]
   ```

3. **Engineer** completes work with the specialist agent in that session.

4. **Engineer** returns to the Orchestrator session and provides the result:

   ```
   @orchestrator — [agent-name] handoff complete:
   What changed: [summary]
   Files modified: [list]
   Test outcome: [pass/fail/partial]
   MEMORY_LOG entries added: [list]
   ```

5. **Orchestrator** continues the lifecycle from that point.

**Impact:** Manual context-switching; team overhead. This is the primary cross-IDE gap.
Log every use to `MEMORY_LOG.md` as type `PATTERN` to track frequency.

---

### `search_web` (`fetch_webpage`)

**When unavailable in:** IntelliJ (fetch_webpage is VS Code-only)

**Fallback:**

1. Engineer looks up the relevant documentation, API reference, or release note.
2. Engineer copies the relevant excerpt and pastes it into Copilot Chat:
   ```
   Here is the relevant documentation for [topic]:
   [paste excerpt]
   ```
3. Agent uses the provided content.

**Impact:** Minimal; most relevant information for day-to-day tasks is in-repository.

---

### `view_image`

**When unavailable in:** IntelliJ

**Fallback:**

1. Engineer describes the image content or extracts relevant text.
2. For UI mockups: describe layout, components, colors, and interaction patterns.
3. For screenshots: describe the error message, stack trace, or visible content.

**Impact:** Minimal; image files are not common in most code repositories.

---

### `ask_clarifying_questions` (Structured)

**When `vscode_askQuestions` is unavailable in:** IntelliJ

**Fallback:**

- Agent asks questions as natural-language text in Copilot Chat.
- Engineer responds in chat.

**Impact:** None for task completion; slightly different UX than VS Code's structured prompts.

---

## Fallback Decision Tree

```
Agent needs capability X
         │
         ├─ Is X in tool-mapping.md as "Full" for current IDE?
         │   └─ YES → Use primary tool directly
         │
         ├─ Is X in tool-mapping.md as "Validate" for current IDE?
         │   └─ Try the tool. If it works → use it. If not → use fallback below.
         │
         └─ Is X in tool-mapping.md as "Missing" for current IDE?
             └─ Use fallback from this document
             └─ Log to MEMORY_LOG.md as PATTERN
             └─ If fallback fails → ask engineer to provide context manually
```

---

## Reporting New Gaps

If you encounter a tool that does not work as expected in either IDE:

1. Log to `MEMORY_LOG.md` as type `ISSUE` or `PATTERN`.
2. Update `tool-mapping.md` with the accurate status.
3. Add a fallback strategy in this file if one does not already exist.
4. Bring it up in the next team discussion.

Frequent gaps that significantly impact productivity should be escalated to
the Orchestrator as candidates for architecture review.
