---
id: clarify
role: analyst
purpose: Clarify goals, scope, assumptions, and acceptance criteria.
inputs:
  - raw_request
outputs:
  - .docs/work/briefs/brief_{topic}.md
  - .docs/changes/{change_id}/brief.md
optional_lenses:
  - domain
  - change
  - knowledge
done_check:
  - goal_is_clear
  - scope_is_bounded
  - acceptance_is_checkable
---

# Clarify Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/analyst.md}}
Template: {{CONTENT: /workflow_core/templates/brief.md}}

## Instructions

Keep the default output light. Capture what the user wants, why it matters, what is in and out of scope, and how completion will be recognized.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/domain.md}} only when terms or rules are ambiguous.
- Use {{CONTENT: /workflow_core/lenses/change.md}} only when the request should become a tracked change.
- Use {{CONTENT: /workflow_core/lenses/knowledge.md}} only when source material should be preserved.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/briefs/brief_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/brief.md`
- If the request is small and already clear, answer inline and recommend `plan` without forcing a file.

## User Input

{{raw request}}
