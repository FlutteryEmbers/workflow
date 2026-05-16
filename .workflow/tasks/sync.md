---
id: sync
role: steward
purpose: Update living docs, archive durable facts, or organize long-term knowledge.
inputs:
  - source_or_change
outputs:
  - updated_docs
  - .docs/current/{topic}.md
  - .docs/knowledge/wiki/{page}.md
  - .docs/changes/{change_id}/archive.md
user_selectable_lenses:
  - change
  - knowledge
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
Template: {{CONTENT: /.workflow/templates/sync.md}}

## Copilot Add Context

Required:

- #.workflow/tasks/sync.md
- #.workflow/templates/sync.md
- source doc, change record, code-adjacent doc, or knowledge material

User-selected lenses:

- Add #.workflow/lenses/change.md only if the user selects `change`.
- Add #.workflow/lenses/knowledge.md only if the user selects `knowledge`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Use this task to reduce knowledge drift. Update only the living docs needed for the requested sync.

## Lens Suggestions

- Suggest `change` when archiving a tracked change. Do not apply it unless selected by the user.
- Suggest `knowledge` when organizing raw material or wiki pages. Do not apply it unless selected by the user.
- Suggest `language` when stable terminology should be normalized or synced to `.docs/shared/glossary.md`. Do not apply it unless selected by the user.
- Suggest `domain` when shared terms or rules need cleanup. Do not apply it unless selected by the user.
- Suggest `architecture` when confirmed architecture constraints should be recorded. Do not apply it unless selected by the user.

## Output Rules

- Living code-adjacent docs stay in `src/**/MODULE.md`.
- For conversation-driven work, extract stable conclusions from session, options, critique, concept, and shape artifacts; do not preserve full chat transcripts as durable facts.
- Durable facts go to `{WorkflowRoot}/.docs/current/{topic}.md`.
- Knowledge pages go to `{WorkflowRoot}/.docs/knowledge/wiki/`.
- Shared project conventions go to `{WorkflowRoot}/.docs/shared/`.
- With `language` lens, stable terminology goes to `{WorkflowRoot}/.docs/shared/glossary.md`.
- With `architecture` lens, confirmed boundaries and constraints go to `{WorkflowRoot}/.docs/shared/boundaries.md`.
- With `change` lens, write archive notes to `{WorkflowRoot}/.docs/changes/{change_id}/archive.md`.

## User Input

{{doc update, change archive request, knowledge source, or drift concern}}
