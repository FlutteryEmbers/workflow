---
id: shape
role: designer
purpose: Form a direction, concept, architecture, goal update, or session decision.
inputs:
  - clarified_context
outputs:
  - .session/decisions/dec_{topic}.md
  - .session/goal/{vision|target_docs|assumptions|roadmap}.md
user_selectable_lenses:
  - iteration
  - expand
  - distill
  - language
  - domain
  - strategy
  - redteam
  - architecture
done_check:
  - decision_is_named
  - tradeoffs_are_visible
  - target_docs_are_named_when_relevant
---

# Shape Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default: shape in chat, do not load templates, and do not write files.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- `Mode: persist`: write only the requested `.session/decisions/**` or `.session/goal/**` target.
- `Mode: execute`: not valid for this task.

## When To Use

- Use when the user needs a direction, concept, architecture shape, goal update, or session decision.
- Use for vision-driven PoC work where the initial request should be reframed into the smallest useful wedge.

## Do Not Use When

- Do not use only to collect raw context; use `clarify` or `explore`.
- Do not use to judge whether existing code/docs/plan are reasonable; use `review`.
- Do not use when the direction is fixed and the user needs executable steps; use `plan`.
- Do not use to update formal docs directly; use `sync`.

## Expected Output

- `Mode: discuss`: `Reframed Goal`, `Narrowest Useful Wedge`, `Success Criteria`, `Rejected Larger Scope`, tradeoffs, and recommended next step.
- `Mode: persist`: a `.session/decisions/**` decision or `.session/goal/**` update.

## Task Boundary Check

Before shaping, classify the request:

- `fits`: user asks to form a direction, concept, architecture, goal update, or session decision.
- `fits_with_preflight`: user asks to shape based on current code, formal docs, or session context. In `Mode: discuss`, perform read-only preflight first, then shape.
- `wrong_task`: user only asks whether current code/docs are reasonable; recommend `review`.
- `wrong_task`: user has a fixed target and wants implementation steps; recommend `plan`.
- `composite`: user asks to evaluate reasonableness and then design a replacement; recommend `review -> shape`.
- `missing_prerequisite`: `Mode: persist` lacks a `.session/decisions/**` or `.session/goal/**` target.

Preflight is read-only, chat-only, and allowed only in `Mode: discuss`. If preflight finds blockers or unclear source of truth, stop and output Recommended Segments.

## Persist Templates

- Decision: `.workflow/templates/decision.md`
- Goal: `.workflow/templates/goal.md`
- Optional shape detail: `.workflow/templates/shape.md`
- With `expand`: `.workflow/templates/expanded.md`
- With `distill`: `.workflow/templates/distillation.md`

## Copilot Add Context

Required:

- #.workflow/tasks/shape.md
- relevant `.session/goal/*`, `.session/notes/**`, existing decisions, docs, or source files

For `Mode: persist`:

- Add #.workflow/templates/decision.md for `.session/decisions/**`.
- Add #.workflow/templates/goal.md for `.session/goal/**`.
- Provide `Target: .session/decisions/dec_{topic}.md` or `Target: .session/goal/{file}.md`.

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Convert notes, goals, and discussion into a clear session decision or goal update. Decisions may represent direction, scope, plan, constraint, handoff, or review stance. Keep session decisions lighter than formal docs.

For vague or broad goals, first reframe the request. Name the smallest useful wedge that can validate the goal, the success criteria that would make it worth continuing, and the larger scope that is intentionally rejected for now.

## Output Rules

- Default persisted decision path: `{WorkflowRoot}/.session/decisions/dec_{topic}.md`.
- Goal updates go to `{WorkflowRoot}/.session/goal/{vision|target_docs|assumptions|roadmap}.md`.
- Formal long-term docs go through `sync`, not `shape`.
- Target docs should be listed when a decision may later update `docs/**`.

## User Input

{{context, options, goal update, design direction, or concept to shape}}
