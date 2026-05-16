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
  - consistency
  - debug
  - distill
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

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial; ask only when ambiguity would affect file writes, execution, or material scope.
- `Mode: discuss` is default: review in chat, do not load templates, and do not write files.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize lens views in the user's lens order, then give actionable findings or recommended next steps.
- `Mode: persist`: write only the requested `.docs/**` target using one of the persist templates below.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens; split into multiple artifacts if more views are needed.
- `Mode: execute`: not valid for this task; use `build` with an approved plan.

## Persist Templates

- Default: `.workflow/templates/review.md`
- With `redteam`: `.workflow/templates/critique.md`
- With `consistency`: `.workflow/templates/consistency_review.md`

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- target source, docs, behavior claim, or evidence

For `Mode: persist`:

- Add #.workflow/templates/review.md, or #.workflow/templates/critique.md / #.workflow/templates/consistency_review.md when the selected lens requires it.
- Provide a `.docs/**` `Target`.

User-selected lenses:

- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Add #.workflow/lenses/redteam.md only if the user selects `redteam`.
- Add #.workflow/lenses/consistency.md only if the user selects `consistency`.
- Add #.workflow/lenses/distill.md only if the user selects `distill`.
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
- Suggest `consistency` when reviewing conflicts between docs, code, tests, code-adjacent README files, or design intent. Do not apply it unless selected by the user.
- Suggest `distill` when reviewing gaps between current Workflow Lite outputs and a reference document structure. Do not apply it unless selected by the user.
- Suggest `language` when reviewing terminology consistency, output language, translation quality, or readability. Do not apply it unless selected by the user.
- Suggest `domain` when critique should inspect language, story flow, events, boundaries, or rule ownership. Do not apply it unless selected by the user.
- Suggest `test` for verification gaps. Do not apply it unless selected by the user.
- Suggest `architecture` for structural risks. Do not apply it unless selected by the user.
- Suggest `knowledge` for docs or knowledge drift. Do not apply it unless selected by the user.
- Suggest `change` when evidence belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- In `Mode: discuss`, report findings, risks, or critique in chat only.
- Default path: `{WorkflowRoot}/.docs/work/reviews/review_{topic}.md`
- With `redteam` lens, use `.workflow/templates/critique.md`.
- With `consistency` lens, use `.workflow/templates/consistency_review.md` and do not directly modify docs or code.
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`

## User Input

{{code, docs, behavior, claim, or risk to review}}
