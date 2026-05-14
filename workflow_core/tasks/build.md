---
id: build
role: builder
purpose: Apply an approved plan to code and adjacent module documentation.
inputs:
  - plan
outputs:
  - code_changes
  - src/{module}/MODULE.md
optional_lenses:
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

## Instructions

Implement the approved plan. Read relevant code first, keep edits inside the plan's scope, and stop if the plan requires unapproved interface, config, data, or architecture changes.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/test.md}} when behavior changes need explicit verification.
- Use {{CONTENT: /workflow_core/lenses/debug.md}} when implementation depends on diagnosing a failure.
- Use {{CONTENT: /workflow_core/lenses/change.md}} when evidence should be recorded in a tracked change.

## Output Rules

- Code changes live in the project codebase.
- Adjacent living docs stay in `src/**/MODULE.md`.
- With `change` lens, record evidence in `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`.

## User Input

{{approved plan and build constraints}}
