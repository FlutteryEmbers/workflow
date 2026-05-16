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
  - redteam
  - debug
  - language
  - domain
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

Role: {{CONTENT: /.workflow/roles/reviewer.md}}
Template: {{CONTENT: /.workflow/templates/review.md}}

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- #.workflow/templates/review.md
- target source, docs, behavior claim, or evidence

User-selected lenses:

- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Add #.workflow/lenses/redteam.md only if the user selects `redteam`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Add #.workflow/lenses/knowledge.md only if the user selects `knowledge`.
- Add #.workflow/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target and report findings first. Keep review scope explicit and avoid rewriting the solution unless the user asks for a fix plan.

## Lens Suggestions

- Suggest `debug` for bugs or uncertain behavior. Do not apply it unless selected by the user.
- Suggest `redteam` when the user wants a proposal, model, or recommendation challenged. Do not apply it unless selected by the user.
- Suggest `language` when reviewing terminology consistency, output language, translation quality, or readability. Do not apply it unless selected by the user.
- Suggest `domain` when critique should inspect language, story flow, events, boundaries, or rule ownership. Do not apply it unless selected by the user.
- Suggest `test` for verification gaps. Do not apply it unless selected by the user.
- Suggest `architecture` for structural risks. Do not apply it unless selected by the user.
- Suggest `knowledge` for docs or knowledge drift. Do not apply it unless selected by the user.
- Suggest `change` when evidence belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/reviews/review_{topic}.md`
- With `redteam` lens, use `.workflow/templates/critique.md`.
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`

## User Input

{{code, docs, behavior, claim, or risk to review}}
