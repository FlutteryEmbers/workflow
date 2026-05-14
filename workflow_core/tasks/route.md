---
id: route
role: analyst
purpose: Recommend the smallest useful workflow path and optional lenses for a user request.
inputs:
  - user_request
outputs:
  - task_recommendation
user_selectable_lenses:
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

## Copilot Add Context

Required:

- #workflow_core/tasks/route.md

User-selected lenses:

- Do not load lens files for routing unless the user explicitly asks to inspect a lens.
- If no lens is named, use `Lens: none`.

## Instructions

Recommend the smallest useful next task. Do not create files. Copilot may suggest lenses, but must not apply them unless the user explicitly selects or adds them.

## Task Choices

- `clarify`: turn a vague request into scope, goals, and acceptance criteria.
- `explore`: understand code, materials, feasibility, or current behavior.
- `shape`: turn context into a solution shape, interface, rule, or decision.
- `plan`: turn a chosen direction into executable steps.
- `build`: apply an approved plan to code and adjacent module docs.
- `review`: inspect behavior, risks, defects, evidence, or refactor options.
- `sync`: update living docs, archive facts, or organize knowledge.

## Lens Hints

- Suggest `domain` when terms, business rules, or ownership are unclear.
- Suggest `test` when behavior needs stronger verification.
- Suggest `architecture` when boundaries, dependencies, or interfaces matter.
- Suggest `change` when the work is large enough to track as a change.
- Suggest `knowledge` when raw material should become durable knowledge.
- Suggest `debug` when root cause or reproduction is unclear.

## Output Format

```text
Recommended task: <task>
Why: <reason>
Lens: <none | user-selected lens list>
Suggested lenses: <none | lens list with reason>
Suggested path: <task -> task -> task>
```
