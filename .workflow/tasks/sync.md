---
id: sync
role: steward
purpose: Update living docs, archive durable facts, or organize long-term knowledge.
inputs:
  - source_or_change
outputs:
  - updated_docs
  - src/**/README.md
  - docs/{area}/{topic}.md
  - .docs/current/{topic}.md
  - .docs/knowledge/wiki/{page}.md
  - .docs/changes/{change_id}/archive.md
user_selectable_lenses:
  - change
  - knowledge
  - consistency
  - publish
  - distill
  - language
  - domain
  - architecture
done_check:
  - source_of_truth_is_named
  - stale_information_is_removed
  - durable_facts_are_traceable
---

# Sync Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial; ask only when ambiguity would affect file writes, execution, target, or source of truth.
- `Mode: discuss` is default: recommend sync targets in chat, do not load templates, and do not write files.
- `Mode: persist`: write only `.docs/**`, explicit `src/**/README.md` code-adjacent README targets, or `docs/**` when `Lens: publish` is selected.
- In `Mode: persist`, confirm the target is `.docs/**`, explicit `src/**/README.md`, or `docs/**` only when `Lens: publish` is selected.
- `Mode: execute`: not valid for this task; use `build` with an approved plan.

## Persist Templates

- Default: `.workflow/templates/sync.md`
- For code-adjacent README targets: `.workflow/templates/code_readme.md`
- For new official docs publish targets: `.workflow/templates/publish_note.md` may be used as a light starting point.

## Copilot Add Context

Required:

- #.workflow/tasks/sync.md
- source doc, change record, code-adjacent doc, or knowledge material

For `Mode: persist`:

- Add #.workflow/templates/sync.md, or #.workflow/templates/code_readme.md when maintaining `src/**/README.md`.
- Add #.workflow/templates/publish_note.md only when publishing a new `docs/**` target with `publish`.
- Provide a `.docs/**` `Target`, `Target: src/**/README.md` for code-adjacent README maintenance, or `Target: docs/**` only with `publish`.

User-selected lenses:

- Add #.workflow/lenses/change.md only if the user selects `change`.
- Add #.workflow/lenses/knowledge.md only if the user selects `knowledge`.
- Add #.workflow/lenses/consistency.md only if the user selects `consistency`.
- Add #.workflow/lenses/publish.md only if the user selects `publish`.
- Add #.workflow/lenses/distill.md only if the user selects `distill`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Use this task to reduce knowledge drift. Update only the living docs needed for the requested sync.

## Lens Suggestions

- Suggest `change` when archiving a tracked change. Do not apply it unless selected by the user.
- Suggest `knowledge` when organizing raw material or wiki pages. Do not apply it unless selected by the user.
- Suggest `consistency` when a review has already confirmed document drift that should be synchronized. Do not apply it unless selected by the user.
- Suggest `publish` when stable `.docs/**` content should become sanitized official project documentation under `docs/**`. Do not apply it unless selected by the user.
- Suggest `distill` when stable reference-derived structures or writing principles should be preserved as knowledge or shared guidance. Do not apply it unless selected by the user.
- Suggest `language` when stable terminology should be normalized or synced to `.docs/shared/glossary.md`. Do not apply it unless selected by the user.
- Suggest `domain` when shared terms or rules need cleanup. Do not apply it unless selected by the user.
- Suggest `architecture` when confirmed architecture constraints should be recorded. Do not apply it unless selected by the user.

## Output Rules

- In `Mode: discuss`, recommend what should be synced and where, without writing files.
- In `Mode: persist`, do not write outside `.docs/**` except for explicit `src/**/README.md` code-adjacent README targets or `docs/**` publish targets with `publish`.
- With `publish` lens, `docs/**` is allowed only when source, target audience, and source of truth are clear. If any are missing, or if sensitive or unresolved content cannot be safely separated, output `Publish blocked` and do not write `docs/**`.
- Do not copy `.docs/**` directly into `docs/**`; publish a sanitized view for the official project documentation audience.
- Code-adjacent README files are optional local reading entrypoints at `src/**/README.md`.
- For conversation-driven work, extract stable conclusions from session, options, critique, concept, and shape artifacts; do not preserve full chat transcripts as durable facts.
- Durable facts go to `{WorkflowRoot}/.docs/current/{topic}.md`.
- Knowledge pages go to `{WorkflowRoot}/.docs/knowledge/wiki/`.
- Shared project conventions go to `{WorkflowRoot}/.docs/shared/`.
- Use `src/**/README.md` only for local code reading help; use `.docs/current/**` for current system facts, `.docs/shared/boundaries.md` for cross-module constraints, `.docs/shared/glossary.md` for stable terms, and `.docs/knowledge/wiki/**` for reusable knowledge.
- With `consistency` lens, sync only confirmed document drift. If code may be wrong, route to `plan -> build`; if intent is unclear, route to `shape`.
- With `publish` lens, remove AI discussion residue, unresolved tradeoffs, rejected options, internal migration notes, temporary workarounds, sensitive implementation detail, security-sensitive detail, and unannounced plans.
- With `distill` lens, preserve stable distillation results only in `.docs/knowledge/wiki/**` or `.docs/shared/**`; do not modify `.workflow/**`.
- With `language` lens, stable terminology goes to `{WorkflowRoot}/.docs/shared/glossary.md`.
- With `architecture` lens, confirmed boundaries and constraints go to `{WorkflowRoot}/.docs/shared/boundaries.md`.
- With `change` lens, write archive notes to `{WorkflowRoot}/.docs/changes/{change_id}/archive.md`.

## User Input

{{doc update, change archive request, knowledge source, or drift concern}}
