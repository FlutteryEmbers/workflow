---
id: review
role: reviewer
purpose: Review behavior, evidence, risks, defects, or refactor opportunities.
inputs:
  - target_or_claim
outputs:
  - .docs/work/reviews/review_{topic}.md
  - .docs/changes/{change_id}/evidence.md
user_selectable_lenses:
  - debug
  - test
  - architecture
  - knowledge
  - change
done_check:
  - findings_are_actionable
  - severity_or_confidence_is_clear
  - evidence_is_recorded
---

# Review Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/reviewer.md}}
Template: {{CONTENT: /workflow_core/templates/review.md}}

## Copilot Add Context

Required:

- #workflow_core/tasks/review.md
- #workflow_core/templates/review.md
- target source, docs, behavior claim, or evidence

User-selected lenses:

- Add #workflow_core/lenses/debug.md only if the user selects `debug`.
- Add #workflow_core/lenses/test.md only if the user selects `test`.
- Add #workflow_core/lenses/architecture.md only if the user selects `architecture`.
- Add #workflow_core/lenses/knowledge.md only if the user selects `knowledge`.
- Add #workflow_core/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target and report findings first. Keep review scope explicit and avoid rewriting the solution unless the user asks for a fix plan.

## Lens Suggestions

- Suggest `debug` for bugs or uncertain behavior. Do not apply it unless selected by the user.
- Suggest `test` for verification gaps. Do not apply it unless selected by the user.
- Suggest `architecture` for structural risks. Do not apply it unless selected by the user.
- Suggest `knowledge` for docs or knowledge drift. Do not apply it unless selected by the user.
- Suggest `change` when evidence belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/reviews/review_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`

## User Input

{{code, docs, behavior, claim, or risk to review}}
