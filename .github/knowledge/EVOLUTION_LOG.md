# Agent Evolution Log

Maintained by the Critic agent. Tracks proposed and applied changes to agent behavior. Agent file changes require explicit Orchestrator approval and audit attribution.

## Entry Format

| Date | Agent | Issue | Suggested Improvement | Approver | Applied by | Applied? |
| ---- | ----- | ----- | --------------------- | -------- | ---------- | -------- |

- **Approver:** Required for `.agent.md` changes; otherwise `n/a`
- **Applied by:** Agent that applied the change; required for `.agent.md` changes, otherwise `n/a`
- **Applied?:** `yes`, `no`, or `pending-approval`

## Log

| Date       | Agent           | Issue                                                                                                                                 | Suggested Improvement                                                                                                                                                                                | Approver     | Applied by       | Applied? |
| ---------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ---------------- | -------- |
| 2026-04-06 | all             | Template baseline established for starter workflow                                                                                    | Initialize evolution tracking with a clean baseline row                                                                                                                                              | n/a          | n/a              | yes      |
| 2026-04-06 | critic          | Low-risk workflow tasks were slower than needed and starter docs mixed reusable and project-local concerns                            | Add complexity triage with fast-track guidance in policy and orchestrator instructions, and formalize generic vs project-specific doc initialization model                                           | orchestrator | backend-engineer | yes      |
| 2026-04-07 | product-manager | No explicit backlog file existed; scope creep items had no structured destination                                                     | Added `docs/BACKLOG.md` with formal Backlog Protocol in AGENTS.md. PM now owns backlog grooming. Planning phase reads backlog, mission, and delivery plan.                                           | orchestrator | backend-engineer | yes      |
| 2026-04-07 | critic          | Critic had no quantitative trigger criteria for proposing improvements — relied on subjective evaluation                              | Added mandatory proposal triggers (consecutive fail/partial, template misses, low quality scores, recurring bugs) and rollback trigger with ROLLBACK entry type                                      | orchestrator | backend-engineer | yes      |
| 2026-04-07 | architect       | Role lacked explicit threat modeling and security architecture ownership; security was distributed with no clear Planning-phase owner | Expanded Architect role to own threat modeling and security architecture review during Planning. Added security gate rule and threat modeling workflow step to architect.agent.md.                   | orchestrator | backend-engineer | yes      |
| 2026-04-07 | user-proxy      | User Proxy only participated post-implementation (Critique phase); UX issues caught too late causing rework                           | Expanded User Proxy to participate in Planning phase for early UX and accessibility requirements review. Added Planning workflow section to user-proxy.agent.md and updated Orchestrator delegation. | orchestrator | backend-engineer | yes      |
