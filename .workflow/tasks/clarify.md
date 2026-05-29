---
id: clarify
role: analyst
purpose: Clarify staged requirements, background, scope, and acceptance notes in chat.
inputs:
  - user_goal_or_context
outputs:
  - chat_clarification
  - suggested_save
user_selectable_lenses:
  - iteration
  - language
  - domain
done_check:
  - goal_is_clear
  - scope_is_named
  - open_questions_are_listed
---

# Clarify Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/analyst.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default and is the only valid mode for this task.
- Do not load templates and do not write files.
- If the user asks to save or provides a target, return `Suggested Save` and route the write to `save`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user is providing goals, staged requirements, background, scope, assumptions, acceptance signals, or unresolved questions.
- Use before `shape` when the target is still ambiguous or the success criteria are not stated.

## Do Not Use When

- Do not use to choose a direction; use `shape`.
- Do not use to evaluate code, docs, plans, or diffs; use `review`.
- Do not use to produce implementation steps; use `plan`.
- Do not use to write session artifacts; use `save`.
- Do not use to update formal docs; use `sync`.

## Expected Output

- Concise clarified goal, scope, acceptance signals, assumptions, and open questions.
- `Suggested Save` when the clarification should become an inbox artifact.

## Task Boundary Check

Before clarifying, classify obvious boundary problems:

- `fits`: user is providing goals, staged requirements, scope, assumptions, or acceptance notes.
- `composite`: user asks to clarify and save; clarify first, then route to `save`.
- `wrong_task`: user asks to judge code/docs reasonableness; recommend `review`.
- `wrong_task`: user asks to form a direction or decision; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.

No implicit preflight runs in `clarify`. Only run Task Boundary Check; do not scan evidence before clarifying.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/clarify.md
- user request, existing `.session/goal/*`, or relevant background

User-selected lenses:

- Add #.workflow/lenses/iteration.md only if the user selects `iteration`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Capture the user's goal, staged requirements, background, constraints, non-goals, acceptance signals, and open questions. Keep this as chat output until the user chooses `save`.

## Suggested Save

When useful, end with:

```text
Suggested Save:
Artifact: brief | note
Status: inbox
Style: summary | exploration
Topic: <topic>
Suggested Target: .session/inbox/<artifact>_<topic>.md
```

## User Input

{{goal, context, staged requirements, or scope question}}
