---
id: clarify
role: analyst
purpose: Clarify staged requirements, background, scope, and acceptance notes in chat.
inputs:
  - user_goal_or_context
outputs:
  - chat_clarification
  - persist_hint
  - full_persist_packet
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

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user is providing goals, staged requirements, background, scope, assumptions, acceptance signals, or unresolved questions.
- Use before `shape` when the target is still ambiguous or the success criteria are not stated.

## Do Not Use When

- Do not use to choose a direction; use `shape`.
- Do not use to evaluate code, docs, plans, or diffs; use `review`.
- Do not use to produce implementation steps; use `plan`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.

## Expected Output

- Concise clarified goal, scope, acceptance signals, assumptions, and open questions.
- `Output: compact` default: short answer and optional `Persist Candidate`.
- `Full Persist Packet` only when the clarification should be persisted now or `Output: full` is requested.

## Task Boundary Check

Before clarifying, classify obvious boundary problems:

- `fits`: user is providing goals, staged requirements, scope, assumptions, or acceptance notes.
- `composite`: user asks to clarify and persist; clarify first, then route to `persist`.
- `wrong_task`: user asks to judge code/docs reasonableness; recommend `review`.
- `wrong_task`: user asks to form a direction or decision; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

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

Capture the user's goal, staged requirements, background, constraints, non-goals, acceptance signals, and open questions. Keep this as chat output until the user chooses `persist`.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants clarified>
Current Read: <optional one line about relevant known context>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Persist Candidate: Artifact=<brief|note>; Artifact State=inbox; Topic=<topic>; Suggested Target=.session/inbox/<artifact>_<topic>.md
```

Use `Persist Candidate: none` when the clarification is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to整理, refine, or prepare for persist without writing files:

```text
User Intent: <one line about what the user wants clarified>
Current Read: <optional one line about relevant known context>
Refined Direction / Plan:
- <clarified goal, scope, acceptance signal, and constraints>
Discussion Notes To Preserve:
- <user correction, staged requirement, constraint, example, or non-goal worth preserving>
Open Questions:
- <question>
Persist Candidate:
- Artifact=<brief|note>; Artifact State=inbox; Topic=<topic>; Suggested Target=.session/inbox/<artifact>_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs a handoff artifact:

```text
Persist Packet:
Artifact: brief | note
Artifact State: inbox
Intent: summary | exploration
Depth: compact | standard
Topic: <topic>
Suggested Target: .session/inbox/<artifact>_<topic>.md
Source Context:
- <user goal, background, or staged requirement source>
Key Points:
- <clarified goal, scope, acceptance signal, or constraint>
Decision-Relevant Facts:
- <facts worth preserving for later shape/plan>
Decision Trail:
- <how the clarification changed the understanding, or none>
Rejected Options:
- <non-goals or excluded scope>
Risks / Unknowns:
- <unknowns or assumptions>
Examples / Pseudocode:
- <example if useful, otherwise none>
Next Use:
- <explore | shape | plan | persist | sync>
```

If the clarification is not worth preserving, output `Persist Candidate: none`.

## User Input

{{goal, context, staged requirements, or scope question}}
