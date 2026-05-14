---
id: plan
role: designer
purpose: Convert a clear goal or solution shape into executable steps.
inputs:
  - brief_or_shape
outputs:
  - .docs/work/plans/plan_{topic}.md
  - .docs/changes/{change_id}/plan.md
user_selectable_lenses:
  - test
  - architecture
  - change
done_check:
  - steps_are_executable
  - verification_is_named
  - scope_boundaries_are_preserved
---

# Plan Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/designer.md}}
Template: {{CONTENT: /workflow_core/templates/plan.md}}

## Copilot Add Context

Required:

- #workflow_core/tasks/plan.md
- #workflow_core/templates/plan.md
- relevant brief, shape, review, or requested work

User-selected lenses:

- Add #workflow_core/lenses/test.md only if the user selects `test`.
- Add #workflow_core/lenses/architecture.md only if the user selects `architecture`.
- Add #workflow_core/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest plan that another agent or engineer can execute without making product decisions.

## Lens Suggestions

- Suggest `test` when verification needs stronger structure. Do not apply it unless selected by the user.
- Suggest `architecture` when the plan touches boundaries or interfaces. Do not apply it unless selected by the user.
- Suggest `change` when the plan belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/plans/plan_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/plan.md`
- Prefer a short executable plan over a comprehensive document.

## User Input

{{brief, shape, review finding, or requested work}}
