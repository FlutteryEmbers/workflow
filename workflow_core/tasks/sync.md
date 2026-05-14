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
optional_lenses:
  - change
  - knowledge
  - domain
  - architecture
done_check:
  - source_of_truth_is_named
  - stale_information_is_removed
  - durable_facts_are_traceable
---

# Sync Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/steward.md}}
Template: {{CONTENT: /workflow_core/templates/sync.md}}

## Instructions

Use this task to reduce knowledge drift. Update only the living docs needed for the requested sync.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/change.md}} when archiving a tracked change.
- Use {{CONTENT: /workflow_core/lenses/knowledge.md}} when organizing raw material or wiki pages.
- Use {{CONTENT: /workflow_core/lenses/domain.md}} when shared terms or rules need cleanup.
- Use {{CONTENT: /workflow_core/lenses/architecture.md}} when confirmed architecture constraints should be recorded.

## Output Rules

- Living code-adjacent docs stay in `src/**/MODULE.md`.
- Durable facts go to `{WorkflowRoot}/.docs/current/{topic}.md`.
- Knowledge pages go to `{WorkflowRoot}/.docs/knowledge/wiki/`.
- Shared project conventions go to `{WorkflowRoot}/.docs/shared/`.
- With `architecture` lens, confirmed boundaries and constraints go to `{WorkflowRoot}/.docs/shared/boundaries.md`.
- With `change` lens, write archive notes to `{WorkflowRoot}/.docs/changes/{change_id}/archive.md`.

## User Input

{{doc update, change archive request, knowledge source, or drift concern}}
