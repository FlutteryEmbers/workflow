---
id: plan
role: designer
purpose: Convert a clear goal or solution shape into executable steps.
inputs:
  - brief_or_shape
outputs:
  - .docs/work/plans/plan_{topic}.md
  - .docs/changes/{change_id}/plan.md
optional_lenses:
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

## Instructions

Write the smallest plan that another agent or engineer can execute without making product decisions.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/test.md}} when verification needs stronger structure.
- Use {{CONTENT: /workflow_core/lenses/architecture.md}} when the plan touches boundaries or interfaces.
- Use {{CONTENT: /workflow_core/lenses/change.md}} when the plan belongs to a tracked change.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/plans/plan_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/plan.md`
- Prefer a short executable plan over a comprehensive document.

## User Input

{{brief, shape, review finding, or requested work}}
