---
id: shape
role: designer
purpose: Shape a solution, rule, contract, architecture decision, or durable direction.
inputs:
  - brief_or_notes
outputs:
  - .docs/work/shapes/shape_{topic}.md
  - .docs/work/decisions/dec_{topic}.md
user_selectable_lenses:
  - domain
  - architecture
  - test
  - change
done_check:
  - decision_is_explicit
  - tradeoffs_are_recorded
  - plan_inputs_are_ready
---

# Shape Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/designer.md}}
Template: {{CONTENT: /workflow_core/templates/shape.md}}

## Copilot Add Context

Required:

- #workflow_core/tasks/shape.md
- #workflow_core/templates/shape.md
- relevant brief, note, code, or decision context

User-selected lenses:

- Add #workflow_core/lenses/domain.md only if the user selects `domain`.
- Add #workflow_core/lenses/architecture.md only if the user selects `architecture`.
- Add #workflow_core/lenses/test.md only if the user selects `test`.
- Add #workflow_core/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Turn clarified context into a solution shape that is detailed enough for planning but not overloaded with implementation trivia.

## Lens Suggestions

- Suggest `domain` for business rules, terms, states, or ownership. Do not apply it unless selected by the user.
- Suggest `architecture` for interfaces, boundaries, dependencies, and expensive decisions. Do not apply it unless selected by the user.
- Suggest `test` when behavior needs testable framing. Do not apply it unless selected by the user.
- Suggest `change` when this shape belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/shapes/shape_{topic}.md`
- Important durable decisions may also be recorded at `{WorkflowRoot}/.docs/work/decisions/dec_{topic}.md`
- Do not force contract, domain, or architecture detail unless a lens is enabled.

## User Input

{{brief, notes, code context, or design question}}
