---
id: clarify
role: analyst
purpose: Clarify user requests, prior AI answers, terms, statements, assumptions, scope boundaries, and success criteria in chat.
inputs:
  - user_goal_context_term_or_statement
outputs:
  - chat_clarification
  - persist_hint
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
- If the user asks to persist or provides a target, return a `Persist Candidate` and route the write to `persist`; do not construct an intermediate packet.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user asks what a term, phrase, prior AI answer, or workflow rule means.
- Use when the user asks to explain, restate, unpack, simplify, or compare meanings.
- Use when the user is providing goals, staged requirements, background, scope, assumptions, acceptance signals, or unresolved questions.
- Use before `shape` when the target is still ambiguous or the success criteria are not stated.
- Typical triggers: "what does this mean", "explain", "clarify", "restate", "how should I understand", "what is the difference", "what assumption is hidden here", or "what is in/out of scope".

## Do Not Use When

- Do not use to choose a direction; use `shape`.
- Do not use to gather or audit source evidence; use `explore`.
- Do not use to summarize specified source material; use `distill`.
- Do not use to evaluate whether code, docs, plans, diffs, or prior AI answers are correct, reasonable, conflicting, safe, or ready; use `review`.
- Do not use to produce implementation steps; use `plan`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.

## Boundary Layers

- `Core Responsibility`: clarify meaning, request boundaries, assumptions, scope, success criteria, and differences without judging correctness.
- `Adjacent Allowance`: include a lightweight next-task hint when the clarification reveals whether the user likely needs `explore`, `distill`, `shape`, `review`, `plan`, `persist`, or `sync`.
- `Forbidden Authority`: do not perform evidence audit, specified-source summary, direction choice, gate verdict, implementation sequencing, stable sync, file write, execution, or implementation.

Adjacent allowance must stay secondary to the clarification. If the user primarily wants the hinted next task, include `Boundary Advice`; provide a useful clarification when one is still safe, and return boundary-only output only for write, sync, execution, or no-useful-clarification cases.

## Result Requirements

- Concise semantic unpack, clarified request, assumptions, scope boundaries, and open questions.
- `Recommended Next Task` or `Next` when the clarification naturally exposes the next workflow step.
- One standard chat response using the shared response groups below.
- `Persist Candidate` only when the clarification is worth preserving.

## Task Boundary Check

Before clarifying, classify obvious boundary problems:

- `fits`: user asks for meaning, restatement, explanation, difference, assumptions, scope, staged requirements, or acceptance notes.
- `composite`: user asks to clarify and persist; clarify first, then route to `persist`.
- `wrong_task`: user asks to gather evidence, inspect files, or discover how something works; recommend `explore`.
- `wrong_task`: user asks to summarize or distill specified source material; recommend `distill`.
- `wrong_task`: user asks whether something is correct, reasonable, conflicting, safe, ready, or worth changing; recommend `review`, using `Change Assessment` for change-seeking judgment.
- `wrong_task`: user asks to form a direction or decision; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

No implicit preflight runs in `clarify`. Only run Task Boundary Check; do not scan evidence before clarifying.

Boundary handling:

- `fits`: clarify in chat.
- `composite`: clarify first, then output the `persist` follow-up prompt; do not write files.
- `wrong_task` or `missing_prerequisite`: output `Boundary Advice`. Still provide a useful in-shape clarification when safe; return only boundary advice, recommended path, and next prompt when clarification would be misleading or the request requires write, sync, execution, or another task's authority.

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

- Always include exactly one short `Example` in the standard response.
- Prefer examples from the current workflow, current user context, or the term being clarified.
- If domain facts are uncertain, use a generic illustrative example and do not introduce new domain claims.
- Use a contrast example when the user asks about the difference between concepts.
- The example explains meaning; it does not prove correctness or act as a review verdict.

## Response Contract

Use one standard response. Keep the shared groups in order, preserve the
task-specific field names, and omit optional groups that have no content:

```text
User Intent
- <one line about what the user wants clarified>
Task State
- Current Read: <optional relevant known context>
- Boundary Advice: <Boundary, Why, Useful Response Now, Advisory Next Task; omit when it adds no value>
- Term / Statement: <term, phrase, prior answer, request, or none>
Primary Result
- Plain Meaning: <plain-language explanation>
- In This Workflow: <workflow-specific meaning, or not workflow-specific>
Supporting Information
- Why It Matters: <why this affects later work; optional>
- Common Confusion: <likely confusion or contrast>
- Example: <exactly one short example; use contrast when helpful>
- Related Concepts: <optional related term or task boundary>
- Clarifying Question: <optional single high-impact question>
- Discussion Notes To Preserve: <optional user correction, constraint, example, boundary, or non-goal>
- Open Questions: <optional unresolved meaning or scope question>
Next
- Recommended Next Task: <clarify|explore|distill|shape|review|plan|persist|sync|none>
- Next: <how to use this clarification or why the next task fits>
Persistence
- Persist Candidate: Artifact=<brief|note>; Artifact State=inbox; Topic=<topic>; Suggested Target=.session/inbox/<artifact>_<topic>.md
```

Omit `Persistence` when the clarification is not worth preserving. A request for
more detail expands these same fields; it does not select another response mode
or load an artifact template.

## User Input

{{term, statement, prior AI answer, goal, context, staged requirements, or scope question}}
