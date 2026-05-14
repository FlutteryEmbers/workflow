---
id: shape
role: designer
purpose: Shape a solution, rule, contract, architecture decision, or durable direction.
inputs:
  - brief_or_notes
outputs:
  - .docs/work/shapes/shape_{topic}.md
  - .docs/work/decisions/dec_{topic}.md
optional_lenses:
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

## Instructions

Turn clarified context into a solution shape that is detailed enough for planning but not overloaded with implementation trivia.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/domain.md}} for business rules, terms, states, or ownership.
- Use {{CONTENT: /workflow_core/lenses/architecture.md}} for interfaces, boundaries, dependencies, and expensive decisions.
- Use {{CONTENT: /workflow_core/lenses/test.md}} when behavior needs testable framing.
- Use {{CONTENT: /workflow_core/lenses/change.md}} when this shape belongs to a tracked change.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/shapes/shape_{topic}.md`
- Important durable decisions may also be recorded at `{WorkflowRoot}/.docs/work/decisions/dec_{topic}.md`
- Do not force contract, domain, or architecture detail unless a lens is enabled.

## User Input

{{brief, notes, code context, or design question}}
