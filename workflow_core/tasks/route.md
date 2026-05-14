---
id: route
role: analyst
purpose: Recommend the smallest useful workflow path and optional lenses for a user request.
inputs:
  - user_request
outputs:
  - task_recommendation
optional_lenses:
  - domain
  - test
  - architecture
  - change
  - knowledge
  - debug
done_check:
  - recommend_one_next_task
  - explain_when_to_add_lenses
  - keep_small_requests_light
---

# Route Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/analyst.md}}

## Instructions

Recommend the smallest useful next task. Do not create files.

## Task Choices

- `clarify`: turn a vague request into scope, goals, and acceptance criteria.
- `explore`: understand code, materials, feasibility, or current behavior.
- `shape`: turn context into a solution shape, interface, rule, or decision.
- `plan`: turn a chosen direction into executable steps.
- `build`: apply an approved plan to code and adjacent module docs.
- `review`: inspect behavior, risks, defects, evidence, or refactor options.
- `sync`: update living docs, archive facts, or organize knowledge.

## Lens Hints

- `domain`: terms, business rules, or ownership are unclear.
- `test`: behavior needs stronger verification.
- `architecture`: boundaries, dependencies, or interfaces matter.
- `change`: the work is large enough to track as a change.
- `knowledge`: raw material should become durable knowledge.
- `debug`: root cause or reproduction is unclear.

## Output Format

```text
Recommended task: <task>
Why: <reason>
Optional lenses: <none | lens list with reason>
Suggested path: <task -> task -> task>
```
