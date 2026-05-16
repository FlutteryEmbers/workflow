---
id: route
role: analyst
purpose: Recommend the smallest useful workflow path and optional lenses for a user request.
inputs:
  - user_request
outputs:
  - task_recommendation
user_selectable_lenses:
  - iteration
  - expand
  - distill
  - language
  - domain
  - strategy
  - redteam
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

Role: {{CONTENT: /.workflow/roles/analyst.md}}

## Copilot Add Context

Required:

- #.workflow/tasks/route.md

User-selected lenses:

- Do not load lens files for routing unless the user explicitly asks to inspect a lens.
- If no lens is named, use `Lens: none`.

## Instructions

Recommend the smallest useful next task. Do not create files. Copilot may suggest lenses, but must not apply them unless the user explicitly selects or adds them.

## Usage Guide Mode

Use this mode when the user asks how to use this workflow, which task/lens to choose, what order to follow, or what context to add. Do not execute the workflow and do not create files. Return a concise user-facing guide in the chat.

## Task Choices

- `clarify`: turn a vague request into scope, goals, and acceptance criteria.
- `explore`: understand code, materials, feasibility, or current behavior.
- `shape`: turn context into a solution shape, interface, rule, or decision.
- `plan`: turn a chosen direction into executable steps.
- `build`: apply an approved plan to repository artifacts.
- `review`: inspect behavior, risks, defects, evidence, or refactor options.
- `sync`: update living docs, archive facts, or organize knowledge.

## Lens Hints

- Suggest `iteration` when the user is co-designing across multiple turns and adding background progressively.
- Suggest `expand` when a short shape or plan needs details, examples, pseudocode, smaller diagrams, or split parts.
- Suggest `distill` when a reference document, architecture folder, or handbook should be studied for reusable structure and writing principles.
- Suggest `language` when the user asks for full English, translation, terminology consistency, or glossary cleanup.
- Suggest `domain` when terms, business rules, or ownership are unclear.
- Suggest `strategy` when technical routes or design options need comparison.
- Suggest `redteam` when the user wants the current recommendation challenged.
- Suggest `test` when behavior needs stronger verification.
- Suggest `architecture` when boundaries, dependencies, or interfaces matter.
- Suggest `change` when the work is large enough to track as a change.
- Suggest `knowledge` when raw material should become durable knowledge.
- Suggest `debug` when root cause or reproduction is unclear.

## Common Scenario Paths

- Small request: `clarify -> plan -> build`
- Code understanding: `explore -> shape/plan`
- Target design planning: `shape -> explore -> plan -> review -> plan`
- Conversation-driven design: `shape --lens iteration --lens strategy -> review --lens redteam -> shape`
- Expansion: `shape/plan --lens expand -> review --lens redteam -> plan/sync`
- Bug fix: `review --lens debug -> plan -> build`
- Knowledge capture: `explore/sync --lens knowledge`
- Reference distillation: `explore --lens distill -> shape --lens distill --lens strategy -> plan -> build -> review`
- Docs or durable facts: `sync`
- Full English or terminology cleanup: relevant task plus `language`

## Output Format

```text
Recommended task: <task>
Why: <reason>
Lens: <none | user-selected lens list>
Suggested lenses: <none | lens list with reason>
Suggested path: <task -> task -> task>
```

## Usage Guide Output Format

```md
## Recommended Workflow

`<task --lens lens -> task -> task>`

## Why This Path

<short explanation>

## Lens Recommendations

- `<lens>`: <why it helps; say user must select it>

## Add Context

- `.workflow/tasks/<task>.md`
- `.workflow/templates/<template>.md` when producing an artifact
- `.workflow/lenses/<lens>.md` only when selected by the user
- <target files, docs, or existing .docs artifacts>

## Next Prompt

<one concrete prompt the user can send next>
```
