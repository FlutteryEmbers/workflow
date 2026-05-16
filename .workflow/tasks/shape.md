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
  - iteration
  - expand
  - distill
  - language
  - domain
  - strategy
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

Role: {{CONTENT: /.workflow/roles/designer.md}}
Template: {{CONTENT: /.workflow/templates/shape.md}}

## Copilot Add Context

Required:

- #.workflow/tasks/shape.md
- #.workflow/templates/shape.md
- relevant brief, note, code, or decision context

User-selected lenses:

- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Add #.workflow/lenses/iteration.md only if the user selects `iteration`.
- Add #.workflow/lenses/expand.md only if the user selects `expand`.
- Add #.workflow/lenses/distill.md only if the user selects `distill`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/strategy.md only if the user selects `strategy`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/change.md only if the user selects `change`.
- Add #.workflow/templates/expanded.md only when the user selects `expand`.
- Add #.workflow/templates/distillation.md only when the user selects `distill`.
- Add #.workflow/templates/session.md, #.workflow/templates/options.md, or #.workflow/templates/concept.md only when the selected lenses need them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Turn clarified context into a solution shape that is detailed enough for planning but not overloaded with implementation trivia. In conversation-driven design, preserve iteration state first, compare options second, and converge only after the current recommendation is stable enough.

## Lens Suggestions

- Suggest `domain` for language, story flow, events, boundaries, rule ownership, or model iteration. Do not apply it unless selected by the user.
- Suggest `iteration` when the user is progressively adding background, constraints, critiques, or decisions. Do not apply it unless selected by the user.
- Suggest `expand` when a short target design, conceptual design, architecture shape, or decision needs more detail or split parts. Do not apply it unless selected by the user.
- Suggest `distill` when shaping which structures or writing principles from a reference should be adopted, adapted, rejected, or revisited. Do not apply it unless selected by the user.
- Suggest `language` when a shape needs full English, translation, terminology consistency, or glossary-ready terms. Do not apply it unless selected by the user.
- Suggest `strategy` when multiple technical routes should be compared. Do not apply it unless selected by the user.
- Suggest `architecture` for interfaces, boundaries, dependencies, and expensive decisions. Do not apply it unless selected by the user.
- Suggest `test` when behavior needs testable framing. Do not apply it unless selected by the user.
- Suggest `change` when this shape belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/shapes/shape_{topic}.md`
- Important durable decisions may also be recorded at `{WorkflowRoot}/.docs/work/decisions/dec_{topic}.md`
- With `iteration` lens, maintain discussion state with `.workflow/templates/session.md`.
- With `expand` lens, use `.workflow/templates/expanded.md` and write `{base}.expanded.md` plus optional `{base}.part_{topic}.md` in the same directory as the source shape or decision.
- With `distill` lens, use `.workflow/templates/distillation.md` and write `{WorkflowRoot}/.docs/work/shapes/shape_distill_{topic}.md`.
- With `strategy` lens, compare routes with `.workflow/templates/options.md`.
- With `domain` lens for conceptual design, use `.workflow/templates/concept.md` when a concept model is needed.
- Do not force contract, domain, or architecture detail unless a lens is enabled.

## User Input

{{brief, notes, code context, or design question}}
