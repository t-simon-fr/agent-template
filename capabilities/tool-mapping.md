# Tool Mapping

This file maps each abstract capability (defined in `capabilities.md`) to the concrete tools
available in each supported editor + GitHub Copilot integration.

**Purpose:** Agent definitions reference capabilities. This file tells each IDE how to fulfill them.
**Authority:** This file is the single source of truth for tool-to-capability mapping.
Changes require Orchestrator approval and an entry in `.github/knowledge/EVOLUTION_LOG.md`.

---

## Supported Editors

| ID         | Editor                                                       | AI Integration                     |
| ---------- | ------------------------------------------------------------ | ---------------------------------- |
| `vscode`   | Visual Studio Code                                           | GitHub Copilot (VS Code extension) |
| `intellij` | IntelliJ-based IDEs (IntelliJ IDEA, PyCharm, WebStorm, etc.) | GitHub Copilot (JetBrains plugin)  |

---

## Capability → Tool Mappings

```yaml
capabilities:
  read_files:
    tier: 0
    vscode:
      primary: read_file
      supplementary: fetch_webpage
    intellij:
      primary: read_file # Available via Copilot plugin (same tool name)
      supplementary: null # fetch_webpage not available; use search_web fallback for URLs
    fallback: "Engineer opens file and pastes relevant content into chat"
    notes: >
      Both editors support read_file via Copilot. fetch_webpage is VS Code-only;
      for URL-based content in IntelliJ, the engineer must perform the web lookup
      manually and paste the relevant content into the chat.

  search_code_text:
    tier: 0
    vscode:
      primary: grep_search
    intellij:
      primary: grep_search # Available via Copilot plugin
    fallback: "Engineer runs grep/rg manually and provides results"
    notes: "grep_search is consistently available in both editors via Copilot."

  search_code_semantic:
    tier: 1
    vscode:
      primary: semantic_search
      supplementary: grep_search # Fallback within VS Code
    intellij:
      primary: semantic_search # Available via Copilot plugin; quality may vary
      supplementary: grep_search
    fallback: "Degrade to search_code_text (grep_search)"
    notes: >
      Semantic search is model-dependent. Results are approximations. Both editors
      use the same Copilot backend, so quality should be consistent in practice.

  list_directory:
    tier: 0
    vscode:
      primary: list_dir
    intellij:
      primary: list_dir # Available via Copilot plugin
    fallback: "Engineer lists directory manually and provides output"
    notes: "Consistently available in both editors via Copilot."

  implement_code_changes:
    tier: 1
    vscode:
      primary:
        - create_file
        - replace_string_in_file
        - multi_replace_string_in_file
      supplementary:
        - insert_edit_into_file
    intellij:
      primary:
        - create_file # Available via Copilot plugin
        - replace_string_in_file # Available via Copilot plugin
        - multi_replace_string_in_file
      supplementary:
        - insert_edit_into_file
    fallback: >
      Agent produces a unified diff or patch. Engineer applies it using IDE's
      Apply Patch feature (IntelliJ: VCS → Apply Patch) or git apply.
    notes: >
      File creation and editing tools are available in both editors via Copilot.
      IntelliJ's refactoring tools (Rename, Extract Method) can supplement for
      large-scale structural changes.

  run_terminal_commands:
    tier: 1
    vscode:
      primary: run_in_terminal
    intellij:
      primary: run_in_terminal # Available via Copilot plugin (same API)
    fallback: >
      Agent generates the exact command with all flags. Engineer runs it in
      IntelliJ's built-in Terminal (Alt+F12) or external terminal, then pastes output.
    notes: >
      run_in_terminal availability in IntelliJ Copilot should be validated during
      Phase 2 pilot. If unavailable, use the manual fallback and log to MEMORY_LOG.md.

  get_terminal_output:
    tier: 1
    vscode:
      primary: get_terminal_output
    intellij:
      primary: get_terminal_output # Available via Copilot plugin if run_in_terminal is available
    fallback: "Engineer pastes terminal output into chat"
    notes: "Tied to run_terminal_commands availability."

  get_diagnostics:
    tier: 1
    vscode:
      primary: get_errors
    intellij:
      primary: get_errors # Available via Copilot plugin
    fallback: >
      Run the project's build or compile command via run_terminal_commands and
      capture stderr output; or run the project's test command to see failing tests.
    notes: >
      get_errors in IntelliJ may surface IntelliJ inspections rather than
      compiler errors. Validate behavior during Phase 2 pilot.

  manage_memory_artifacts:
    tier: 0
    vscode:
      primary: [read_file, replace_string_in_file, create_file]
      paths:
        [
          ".github/knowledge/MEMORY_LOG.md",
          ".github/knowledge/METRICS.md",
          ".github/knowledge/EVOLUTION_LOG.md",
        ]
    intellij:
      primary: [read_file, replace_string_in_file, create_file]
      paths:
        [
          ".github/knowledge/MEMORY_LOG.md",
          ".github/knowledge/METRICS.md",
          ".github/knowledge/EVOLUTION_LOG.md",
        ]
    fallback: "Engineer manually edits the knowledge file following the documented format"
    notes: >
      Memory artifacts are plain Markdown files committed to the repository.
      Both editors can read and write them via Copilot file tools.
      These files are fully IDE-independent.

  ask_clarifying_questions:
    tier: 0
    vscode:
      primary: vscode_askQuestions # VS Code-specific interactive prompt tool
      supplementary: chat_response # Natural language question in chat
    intellij:
      primary: chat_response # Natural language question in Copilot chat
    fallback: "Ask as natural-language text in chat response"
    notes: >
      vscode_askQuestions provides structured UI prompts in VS Code. IntelliJ
      uses free-form chat. Both achieve the same goal with different UX.

  spawn_subagent:
    tier: 1
    vscode:
      primary: runSubagent # VS Code Copilot multi-agent support
    intellij:
      primary: null # Not available as of 2026-05
      workaround: >
        Engineer manually switches to the target agent in a new Copilot chat,
        provides context from the handoff template, and returns results to caller.
    fallback: "Orchestrator performs work sequentially without delegation"
    notes: >
      Subagent spawning (runSubagent) is a VS Code Copilot-specific feature.
      In IntelliJ, the Orchestrator should either work sequentially or the engineer
      manually facilitates the handoff. This is the primary cross-IDE gap identified.
      Tracked for re-evaluation when IntelliJ Copilot adds multi-agent support.

  search_web:
    tier: 2
    vscode:
      primary: fetch_webpage
    intellij:
      primary: null
      workaround: "Engineer performs web search and pastes relevant content"
    fallback: "Engineer provides relevant documentation excerpt in chat"
    notes: "fetch_webpage is VS Code-only. Low impact; most information is in-repo."

  view_image:
    tier: 2
    vscode:
      primary: view_image
    intellij:
      primary: null
      workaround: "Engineer describes the image or shares relevant text content"
    fallback: "Engineer describes image content; agent proceeds from description"
    notes: "Image viewing requires multimodal model support. Low usage in most code repositories."
```

---

## Cross-IDE Gap Summary

| Capability                 | VS Code |   IntelliJ    |              Gap Severity              |
| -------------------------- | :-----: | :-----------: | :------------------------------------: |
| `read_files`               | ✅ Full |    ✅ Full    |                  None                  |
| `search_code_text`         | ✅ Full |    ✅ Full    |                  None                  |
| `search_code_semantic`     | ✅ Full |    ✅ Full    |                  None                  |
| `list_directory`           | ✅ Full |    ✅ Full    |                  None                  |
| `implement_code_changes`   | ✅ Full |    ✅ Full    |                  None                  |
| `run_terminal_commands`    | ✅ Full |  ⚠️ Validate  |       Medium — validate Phase 2        |
| `get_terminal_output`      | ✅ Full |  ⚠️ Validate  |         Medium — tied to above         |
| `get_diagnostics`          | ✅ Full |  ⚠️ Validate  |    Low — fallback via project build    |
| `manage_memory_artifacts`  | ✅ Full |    ✅ Full    |                  None                  |
| `ask_clarifying_questions` | ✅ Full | ✅ Equivalent |          None (different UX)           |
| `spawn_subagent`           | ✅ Full |  ❌ Missing   | High — Orchestrator workaround needed  |
| `search_web`               | ✅ Full |  ❌ Missing   | Low — rare usage, easy manual fallback |
| `view_image`               | ✅ Full |  ❌ Missing   | Low — rare usage in most repositories  |

### Mitigation for `spawn_subagent` Gap

Until IntelliJ Copilot supports multi-agent delegation:

1. **IntelliJ engineers** working with the Orchestrator should open the target agent in a new chat session, provide the handoff context, and return the result.
2. **Orchestrator running in IntelliJ** should use sequential single-agent workflow (Orchestrator handles all phases itself) or ask the engineer to switch agents manually.
3. Log usage of this workaround in `MEMORY_LOG.md` as type `PATTERN`.

---

## Updating This File

When a tool changes behavior, a new tool is added, or a new IDE is supported:

1. Update the relevant capability entry above.
2. Update the Gap Summary table.
3. Log the change in `.github/knowledge/EVOLUTION_LOG.md`.
4. Update `.github/capabilities/profiles.md` if agent capability profiles change.
5. Obtain Orchestrator approval.
