# Agent Capability Profiles

This file defines the **required capabilities** for each agent as a conceptual reference.
It documents what each agent needs to do its job, mapped to abstract capabilities from
`capabilities.md`.

**Implementation note:** Agent frontmatter (`*.agent.md`) does not use a `tools:` key; tool
access is determined by the IDE and GitHub Copilot automatically based on context. The
capability profiles defined here document what each agent needs conceptually. For the
concrete IDE → capability mapping, see `tool-mapping.md`.

---

## Profile Format

```yaml
agent_name:
  tier: planning | implementation | diagnostic | quality | coordination
  description: "One-line summary"
  required_capabilities: # Must be available; agent cannot function without these
    - capability_name
  preferred_capabilities: # Used when available; agent degrades gracefully without
    - capability_name
  excluded_capabilities: # Explicitly prohibited for this agent
    - capability_name
  notes: "Any cross-IDE considerations"
```

---

## Agent Profiles

```yaml
orchestrator:
  tier: coordination
  description: "Lifecycle management, task decomposition, agent delegation"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - manage_memory_artifacts
    - ask_clarifying_questions
  preferred_capabilities:
    - search_code_semantic
    - spawn_subagent
  excluded_capabilities:
    - implement_code_changes # Orchestrator delegates; does not implement
    - run_terminal_commands # Delegates to implementation agents
  notes: >
    spawn_subagent is the primary cross-IDE gap for Orchestrator.
    In IntelliJ, the engineer manually facilitates agent handoffs.
    See tool-mapping.md for the workaround.

product_manager:
  tier: planning
  description: "User value validation, prioritization, outcome definition"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - manage_memory_artifacts
    - ask_clarifying_questions
  preferred_capabilities:
    - search_code_semantic
    - search_web
  excluded_capabilities:
    - implement_code_changes
    - run_terminal_commands
    - spawn_subagent
  notes: "Read-only agent. No cross-IDE gaps."

business_analyst:
  tier: planning
  description: "Requirements clarification, acceptance criteria, scope definition"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - manage_memory_artifacts
    - ask_clarifying_questions
  preferred_capabilities:
    - search_code_semantic
    - search_web
  excluded_capabilities:
    - implement_code_changes
    - run_terminal_commands
    - spawn_subagent
  notes: "Read-only agent. No cross-IDE gaps."

architect:
  tier: planning
  description: "System design, API contracts, trade-off analysis"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - search_code_semantic
    - manage_memory_artifacts
    - ask_clarifying_questions
  preferred_capabilities:
    - search_web
    - view_image
  excluded_capabilities:
    - implement_code_changes
    - run_terminal_commands
    - spawn_subagent
  notes: "Read-only agent. No significant cross-IDE gaps."

frontend_engineer:
  tier: implementation
  description: "UI implementation, state flows, accessibility, client performance"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - search_code_semantic
    - implement_code_changes
    - run_terminal_commands
    - get_terminal_output
    - get_diagnostics
    - manage_memory_artifacts
  preferred_capabilities:
    - view_image
  excluded_capabilities:
    - spawn_subagent
  notes: >
    run_terminal_commands and get_diagnostics should be validated in IntelliJ
    during Phase 2. Fallback: engineer runs commands and pastes output.

backend_engineer:
  tier: implementation
  description: "API/domain implementation, validation, security hardening, data access"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - search_code_semantic
    - implement_code_changes
    - run_terminal_commands
    - get_terminal_output
    - get_diagnostics
    - manage_memory_artifacts
  preferred_capabilities: []
  excluded_capabilities:
    - spawn_subagent
    - view_image
  notes: >
    Primary implementation agent. run_terminal_commands used heavily for
    project builds and test execution. Validate IntelliJ behavior during Phase 2.

qa_engineer:
  tier: implementation
  description: "Test design, test generation, regression checks, defect reporting"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - search_code_semantic
    - implement_code_changes
    - run_terminal_commands
    - get_terminal_output
    - get_diagnostics
    - manage_memory_artifacts
  preferred_capabilities: []
  excluded_capabilities:
    - spawn_subagent
    - view_image
  notes: >
    Runs the project's test suites. run_terminal_commands
    is critical. Validate IntelliJ terminal integration during Phase 2.

debugger:
  tier: diagnostic
  description: "Root-cause analysis, stack-trace investigation, performance profiling"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - search_code_semantic
    - run_terminal_commands
    - get_terminal_output
    - get_diagnostics
    - manage_memory_artifacts
  preferred_capabilities:
    - search_web
  excluded_capabilities:
    - implement_code_changes # Debugger diagnoses; does not fix
    - spawn_subagent
    - view_image
  notes: >
    Diagnostic agent; run_terminal_commands used for log tailing and tool execution.
    Must reproduce before diagnosing. Validate IntelliJ terminal tools in Phase 2.

critic:
  tier: quality
  description: "Code quality review, test coverage audit, self-improvement tracking"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - search_code_semantic
    - get_diagnostics
    - manage_memory_artifacts
  preferred_capabilities: []
  excluded_capabilities:
    - implement_code_changes
    - run_terminal_commands
    - spawn_subagent
    - view_image
  notes: "Read-only agent. No significant cross-IDE gaps."

user_proxy:
  tier: quality
  description: "End-user perspective, usability review, accessibility, UX friction"
  required_capabilities:
    - read_files
    - search_code_text
    - list_directory
    - manage_memory_artifacts
  preferred_capabilities:
    - view_image
    - search_code_semantic
  excluded_capabilities:
    - implement_code_changes
    - run_terminal_commands
    - spawn_subagent
  notes: "Read-only agent. No significant cross-IDE gaps."
```

---

## Cross-IDE Impact by Agent

| Agent             | Tier           | IntelliJ Impact              | Action                                 |
| ----------------- | -------------- | ---------------------------- | -------------------------------------- |
| orchestrator      | coordination   | `spawn_subagent` unavailable | Engineer manually facilitates handoffs |
| product_manager   | planning       | None                         | No action needed                       |
| business_analyst  | planning       | None                         | No action needed                       |
| architect         | planning       | None                         | No action needed                       |
| frontend_engineer | implementation | Terminal tools — validate    | Phase 2 validation                     |
| backend_engineer  | implementation | Terminal tools — validate    | Phase 2 validation                     |
| qa_engineer       | implementation | Terminal tools — validate    | Phase 2 validation                     |
| debugger          | diagnostic     | Terminal tools — validate    | Phase 2 validation                     |
| critic            | quality        | None                         | No action needed                       |
| user_proxy        | quality        | None                         | No action needed                       |

---

## Adding or Updating a Profile

1. Update this file with the new or changed profile.
2. Verify capability definitions exist in `capabilities.md`.
3. Verify tool mappings exist in `tool-mapping.md`.
4. Log the change in `.github/knowledge/EVOLUTION_LOG.md`.
5. Obtain Orchestrator approval.
