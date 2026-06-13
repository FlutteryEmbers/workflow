---
id: clarify
role: analyst
purpose: Clarify user requests, prior AI answers, terms, statements, assumptions, scope boundaries, and success criteria in chat.
inputs:
  - user_goal_context_term_or_statement
outputs:
  - chat_clarification
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - language
done_check:
  - meaning_is_clear
  - scope_or_assumptions_are_named
  - example_is_included
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

- Use when the user asks what a term, phrase, prior AI answer, or workflow rule means.
- Use when the user asks to explain, restate, unpack, simplify, or compare meanings.
- Use when the user is providing goals, staged requirements, background, scope, assumptions, acceptance signals, or unresolved questions.
- Use before `shape` when the target is still ambiguous or the success criteria are not stated.
- Typical triggers: "what does this mean", "explain", "clarify", "restate", "how should I understand", "what is the difference", "what assumption is hidden here", or "what is in/out of scope".

## Do Not Use When

- Do not use to choose a direction; use `shape`.
- Do not use to evaluate whether code, docs, plans, diffs, or prior AI answers are correct, reasonable, conflicting, safe, or ready; use `review`.
- Do not use to produce implementation steps; use `plan`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.

## Expected Output

- Concise semantic unpack, clarified request, assumptions, scope boundaries, and open questions.
- `Output: compact` default: short answer and optional `Persist Candidate`.
- `Full Persist Packet` only when the clarification should be persisted now or `Output: full` is requested.

## Task Boundary Check

Before clarifying, classify obvious boundary problems:

- `fits`: user asks for meaning, restatement, explanation, difference, assumptions, scope, staged requirements, or acceptance notes.
- `composite`: user asks to clarify and persist; clarify first, then route to `persist`.
- `wrong_task`: user asks whether something is correct, reasonable, conflicting, safe, ready, or worth changing; recommend `review`.
- `wrong_task`: user asks to form a direction or decision; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

No implicit preflight runs in `clarify`. Only run Task Boundary Check; do not scan evidence before clarifying.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/clarify.md
- user request, relevant `.session/inbox/**`, existing thread artifacts, or background

User-selected lenses:

- Add #.workflow/lenses/language.md only if the user selects `language`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Clarify meaning without judging correctness. Treat `clarify` as semantic unpacking and request reframing, not review, shape, plan, or implementation.

Supported subflows:

- `Term Unpack`: explain a term or concept in plain language and in Workflow Lite context.
- `Statement Unpack`: explain a sentence, prior AI answer, rule, or claim without judging whether it is correct.
- `Request Reframe`: restate the user's goal, scope, success criteria, assumptions, and open questions.

Example Required:

- Always include exactly one short `Example` in compact output.
- Prefer examples from the current workflow, current user context, or the term being clarified.
- If domain facts are uncertain, use a generic illustrative example and do not introduce new domain claims.
- Use a contrast example when the user asks about the difference between concepts.
- The example explains meaning; it does not prove correctness or act as a review verdict.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants clarified>
Term / Statement: <term, phrase, prior answer, request, or none>
Plain Meaning: <plain-language explanation>
In This Workflow: <what it means in this workflow, or not workflow-specific>
Common Confusion: <likely confusion or none>
Example: <one minimal example; use contrast when helpful>
Next: <how to use this clarification or which task to use next>
Persist Candidate: Artifact=<brief|note>; Artifact State=inbox; Topic=<topic>; Suggested Target=.session/inbox/<artifact>_<topic>.md
```

Use `Persist Candidate: none` when the clarification is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to整理, refine, or prepare for persist without writing files:

```text
User Intent: <one line about what the user wants clarified>
Current Read: <optional one line about relevant known context>
Term / Statement:
- <term, phrase, prior answer, request, or none>
Plain Meaning:
- <plain-language explanation>
In This Workflow:
- <workflow-specific meaning, or not workflow-specific>
Why It Matters:
- <why this affects later discussion, persist, review, shape, plan, sync, or build>
Common Confusion:
- <confusion or contrast>
Example:
- <one minimal example>
Related Concepts:
- <related term or task boundary>
Clarifying Question:
- <none or one high-impact question>
Discussion Notes To Preserve:
- <user correction, staged requirement, constraint, example, meaning boundary, or non-goal worth preserving>
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
- <user goal, background, term, prior AI answer, statement, or staged requirement source>
Key Points:
- <clarified meaning, goal, scope, acceptance signal, or constraint>
Clarified Meaning:
- <plain meaning and workflow-specific meaning>
Assumptions To Confirm:
- <assumption or none>
Scope Boundary:
- <in scope, out of scope, or not applicable>
Example:
- <minimal example used to explain the term, statement, or request>
Non-Goals:
- <non-goals or excluded scope>
Risks / Unknowns:
- <unknowns or assumptions>
Next Use:
- <explore | shape | plan | persist | sync>
```

If the clarification is not worth preserving, output `Persist Candidate: none`.

## User Input

{{term, statement, prior AI answer, goal, context, staged requirements, or scope question}}
