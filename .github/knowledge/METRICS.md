# Observability Metrics

Task-level metrics tracked by all agents. Append after completing each task. Orchestrator reviews periodically to identify bottlenecks and improvement opportunities.

## Entry Format

| task_id | agent | phase | outcome | duration_estimate | quality_score | notes |
| ------- | ----- | ----- | ------- | ----------------- | ------------- | ----- |

- **outcome:** `pass`, `fail`, or `partial`
- **quality_score:** 1–5 (1 = poor, 5 = excellent)

## Metrics

| task_id                            | agent        | phase    | outcome | duration_estimate | quality_score | notes                                                                                     |
| ---------------------------------- | ------------ | -------- | ------- | ----------------- | ------------- | ----------------------------------------------------------------------------------------- |
| TEMPLATE-INIT-20260406             | orchestrator | Learning | pass    | ~15m              | 5             | Bootstrap initialization of project knowledge files for starter use                       |
| DOC-WORKFLOW-OPTIMIZATION-20260406 | orchestrator | Learning | pass    | ~40m              | 5             | Added fast-track triage model and docs split, then re-read all edited files for coherence |
