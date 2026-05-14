# Capability Registry

This file defines the **abstract capabilities** that agents in this repository may use.
Capabilities describe **what agents can do**, not **how** any specific editor or tool implements it.
The mapping from capabilities to editor-specific tools lives in `tool-mapping.md`.

All agent definitions (`.github/agents/`) reference capabilities from this registry.

---

## Capability Tiers

| Tier | Label     | Meaning                                                                     |
| ---- | --------- | --------------------------------------------------------------------------- |
| `0`  | Critical  | Required for any agent to function; must be available in all supported IDEs |
| `1`  | Important | Needed for full agent effectiveness; must have a fallback if unavailable    |
| `2`  | Optional  | Enhances productivity; may be absent; agents degrade gracefully             |

---

## Capability Definitions

### `read_files` (Tier 0 — Critical)

- **Purpose:** Read the contents of files in the repository by path, pattern, or search.
- **Used by:** All agents.
- **Notes:** Must respect `.gitignore`. Must not read credentials or secrets.
- **Fallback:** Manual inspection by engineer if API fails.

---

### `search_code_text` (Tier 0 — Critical)

- **Purpose:** Search repository files using exact text or regex patterns.
- **Used by:** All agents.
- **Notes:** Case-insensitive by default; scope to specific paths when possible.
- **Fallback:** Manual `grep` by engineer.

---

### `search_code_semantic` (Tier 1 — Important)

- **Purpose:** Find code or documentation by meaning, intent, or symbol name rather than exact text.
- **Used by:** Architect, Backend Engineer, Frontend Engineer, Debugger, Critic.
- **Notes:** Results may vary between editors. Used for discovery, not authoritative lookup.
- **Fallback:** Degrade to `search_code_text` if semantic search is unavailable.

---

### `list_directory` (Tier 0 — Critical)

- **Purpose:** List the contents of a directory to understand project structure.
- **Used by:** All agents.
- **Fallback:** Manual directory traversal by engineer.

---

### `implement_code_changes` (Tier 1 — Important)

- **Purpose:** Create, modify, or delete source files and tests.
- **Used by:** Backend Engineer, Frontend Engineer, QA Engineer.
- **Notes:** All changes must comply with project conventions and pass lint/compile. Must not modify `.agent.md` files.
- **Fallback:** Generate a diff or patch; engineer applies it manually.

---

### `run_terminal_commands` (Tier 1 — Important)

- **Purpose:** Execute shell or PowerShell commands (builds, test runners, linters).
- **Used by:** Backend Engineer, Frontend Engineer, QA Engineer, Debugger.
- **Notes:** Never run destructive commands (`rm -rf`, `git push --force`) without explicit user confirmation. Prefer project-defined scripts over ad-hoc commands.
- **Fallback:** Agent generates command; engineer runs it manually and pastes output back.

---

### `get_terminal_output` (Tier 1 — Important)

- **Purpose:** Read the output of a previously executed terminal command.
- **Used by:** Backend Engineer, Frontend Engineer, QA Engineer, Debugger.
- **Notes:** Used in conjunction with `run_terminal_commands` to inspect results.
- **Fallback:** Engineer pastes output into chat; agent analyzes it.

---

### `get_diagnostics` (Tier 1 — Important)

- **Purpose:** Retrieve compile errors, lint warnings, and static analysis results for a file or project.
- **Used by:** Backend Engineer, Frontend Engineer, QA Engineer, Debugger, Critic.
- **Notes:** Results are editor-reported; may differ slightly between IDEs.
- **Fallback:** Run the project's build or compile command via `run_terminal_commands` to capture errors.

---

### `manage_memory_artifacts` (Tier 0 — Critical)

- **Purpose:** Read and write shared knowledge artifacts: `MEMORY_LOG.md`, `METRICS.md`, `EVOLUTION_LOG.md`.
- **Used by:** All agents.
- **Notes:** Implemented via `read_files` + `implement_code_changes` on `.github/knowledge/` paths. These files are committed to the repository and are IDE-independent.
- **Fallback:** Engineer manually edits the file if agent cannot write.

---

### `ask_clarifying_questions` (Tier 0 — Critical)

- **Purpose:** Request additional information from the engineer when requirements are ambiguous.
- **Used by:** All agents, especially Business Analyst, Orchestrator.
- **Notes:** Implemented via natural-language chat response, not a specific tool.
- **Fallback:** N/A — always available.

---

### `spawn_subagent` (Tier 1 — Important)

- **Purpose:** Delegate a subtask to another specialized agent.
- **Used by:** Orchestrator only.
- **Notes:** Implementation depends on IDE/Copilot agent support. Fallback is sequential single-agent work.
- **Fallback:** Orchestrator performs the work itself or requests engineer to switch agent manually.

---

### `search_web` (Tier 2 — Optional)

- **Purpose:** Retrieve current information from external web sources (API docs, release notes, CVE databases).
- **Used by:** Architect, Debugger (research mode).
- **Notes:** Not always available; never used for code generation without human review.
- **Fallback:** Engineer provides relevant documentation manually.

---

### `view_image` (Tier 2 — Optional)

- **Purpose:** Inspect image files (screenshots, diagrams, UI mockups) in the repository.
- **Used by:** Frontend Engineer, User Proxy, Architect.
- **Notes:** Requires multimodal model support.
- **Fallback:** Engineer describes image contents; agent proceeds from description.

---

## Capability Summary Table

| Capability                 | Tier | Read-Only | Write | Execute | Agents                          |
| -------------------------- | ---- | :-------: | :---: | :-----: | ------------------------------- |
| `read_files`               | 0    |    ✅     |   —   |    —    | All                             |
| `search_code_text`         | 0    |    ✅     |   —   |    —    | All                             |
| `search_code_semantic`     | 1    |    ✅     |   —   |    —    | Most                            |
| `list_directory`           | 0    |    ✅     |   —   |    —    | All                             |
| `implement_code_changes`   | 1    |     —     |  ✅   |    —    | Engineers, QA                   |
| `run_terminal_commands`    | 1    |     —     |   —   |   ✅    | Engineers, QA, Debugger         |
| `get_terminal_output`      | 1    |    ✅     |   —   |    —    | Engineers, QA, Debugger         |
| `get_diagnostics`          | 1    |    ✅     |   —   |    —    | Engineers, QA, Debugger, Critic |
| `manage_memory_artifacts`  | 0    |    ✅     |  ✅   |    —    | All                             |
| `ask_clarifying_questions` | 0    |     —     |   —   |    —    | All                             |
| `spawn_subagent`           | 1    |     —     |   —   |   ✅    | Orchestrator                    |
| `search_web`               | 2    |    ✅     |   —   |    —    | Architect, Debugger             |
| `view_image`               | 2    |    ✅     |   —   |    —    | Frontend, User Proxy, Architect |

---

## Adding a New Capability

1. Define the capability here with: purpose, tier, users, notes, fallback.
2. Add its tool mappings in `tool-mapping.md`.
3. Update relevant agent profiles in `profiles.md`.
4. Log the addition to `.github/knowledge/EVOLUTION_LOG.md`.
5. Obtain Orchestrator approval before updating agent definitions.
