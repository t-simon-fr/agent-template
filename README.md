# Project Template

Starter template with a multi-agent Copilot workflow.

## Structure

```
.github/
  AGENTS.md              # Shared policy for all agents
  agents/
    orchestrator.agent.md   # Coordinates specialists
    business-analyst.agent.md
    architect.agent.md
    frontend-engineer.agent.md
    backend-engineer.agent.md
    qa-engineer.agent.md
    debugger.agent.md
  knowledge/
    PROJECT_MISSION.md    # Fill in your project goals
    DELIVERY_PLAN.md      # Fill in your delivery plan
```

## Getting Started

1. Fill in `.github/knowledge/PROJECT_MISSION.md` with your project goals.
2. Fill in `.github/knowledge/DELIVERY_PLAN.md` with your delivery plan.
3. Use the `orchestrator` agent mode in Copilot Chat to coordinate work.
4. Add your project's source code and start building!
