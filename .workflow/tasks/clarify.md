---
id: clarify
role: analyst
purpose: Capture staged requirements, background, scope, and acceptance notes.
inputs:
  - user_goal_or_context
outputs:
  - .session/notes/note_{topic}.md
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
- `Mode: discuss` is default: clarify in chat, do not load templates, and do not write files.
- `Mode: persist`: write only the requested `.session/notes/**` target.
- `Mode: execute`: not valid for this task.

## When To Use

- Use when the user is providing goals, staged requirements, background, scope, assumptions, acceptance signals, or unresolved questions.
- Use before `shape` when the target is still ambiguous or the success criteria are not stated.

## Do Not Use When

- Do not use to choose a direction; use `shape`.
- Do not use to evaluate code, docs, plans, or diffs; use `review`.
- Do not use to produce implementation steps; use `plan`.
- Do not use to update formal docs; use `sync`.

## Expected Output

- `Mode: discuss`: concise clarified goal, scope, acceptance signals, assumptions, and open questions.
- `Mode: persist`: a `.session/notes/**` note that preserves user-provided context without turning it into formal source of truth.

## Task Boundary Check

Before clarifying, classify obvious boundary problems:

- `fits`: user is providing goals, staged requirements, scope, assumptions, or acceptance notes.
- `wrong_task`: user asks to judge code/docs reasonableness; recommend `review`.
- `wrong_task`: user asks to form a direction or decision; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.
- `missing_prerequisite`: `Mode: persist` lacks a `.session/notes/**` target.

No implicit preflight runs in `clarify`. Only run Task Boundary Check; do not scan evidence before clarifying.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Persist Templates

- Default: `.workflow/templates/note.md`

## Copilot Add Context

Required:

- #.workflow/tasks/clarify.md
- user request, existing `.session/goal/*`, or relevant background

For `Mode: persist`:

- Add #.workflow/templates/note.md.
- Provide `Target: .session/notes/note_{topic}.md`.

User-selected lenses:

- Add #.workflow/lenses/iteration.md only if the user selects `iteration`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Capture the user's goal, staged requirements, background, constraints, non-goals, acceptance signals, and open questions. Keep this as session input, not as formal project documentation.

## Output Rules

- Default persisted path: `{WorkflowRoot}/.session/notes/note_{topic}.md`.
- Do not write `docs/**`; use `sync` when a stable formal doc must be updated.
- Do not treat `.session/**` as source of truth.

## User Input

{{goal, context, staged requirements, or scope question}}
