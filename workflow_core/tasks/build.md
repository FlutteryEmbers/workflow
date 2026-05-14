---
id: build
role: builder
purpose: Apply an approved plan to code and adjacent module documentation.
inputs:
  - plan
outputs:
  - code_changes
  - src/{module}/MODULE.md
user_selectable_lenses:
  - test
  - debug
  - change
done_check:
  - plan_was_followed
  - verification_evidence_recorded
  - module_docs_updated_when_needed
---

# Build Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/builder.md}}
Module Template: {{CONTENT: /workflow_core/templates/module_status.md}}

## Copilot Add Context

Required:

- #workflow_core/tasks/build.md
- approved plan file
- target source files

User-selected lenses:

- Add #workflow_core/lenses/test.md only if the user selects `test`.
- Add #workflow_core/lenses/debug.md only if the user selects `debug`.
- Add #workflow_core/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Implement the approved plan. Read relevant code first, keep edits inside the plan's scope, and stop if the plan requires unapproved interface, config, data, or architecture changes.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.
- Suggest `change` when evidence should be recorded in a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Code changes live in the project codebase.
- Adjacent living docs stay in `src/**/MODULE.md`.
- With `change` lens, record evidence in `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`.

## User Input

{{approved plan and build constraints}}
