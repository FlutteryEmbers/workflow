---
id: build
role: builder
purpose: Apply an approved plan to repository artifacts.
inputs:
  - plan
outputs:
  - repository_changes
  - code_or_documentation_updates
user_selectable_lenses:
  - test
  - debug
  - change
done_check:
  - plan_was_followed
  - verification_evidence_recorded
  - artifact_boundaries_preserved
---

# Build Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/builder.md}}
Module Template: {{CONTENT: /.workflow/templates/module_status.md}}

## Copilot Add Context

Required:

- #.workflow/tasks/build.md
- approved plan file
- target source files, docs, prompts, templates, or workflow artifacts

User-selected lenses:

- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Add #.workflow/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Implement the approved plan. Read relevant artifacts first, keep edits inside the plan's scope, and stop if the plan requires unapproved interface, config, data, architecture, or workflow behavior changes.

Build may update code, docs, prompts, templates, or workflow artifacts only when the approved plan explicitly names them. Do not modify `.workflow/**` unless the approved plan targets workflow behavior.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.
- Suggest `change` when evidence should be recorded in a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Repository changes live in the project codebase.
- Workflow system changes live in `.workflow/**` and require an explicit approved plan.
- Adjacent living docs stay in `src/**/MODULE.md`.
- With `change` lens, record evidence in `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`.

## User Input

{{approved plan and build constraints}}
