---
id: clarify
role: analyst
purpose: Clarify goals, scope, assumptions, and acceptance criteria.
inputs:
  - raw_request
outputs:
  - .docs/work/briefs/brief_{topic}.md
  - .docs/changes/{change_id}/brief.md
user_selectable_lenses:
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

## Copilot Add Context

Required:

- #workflow_core/tasks/clarify.md
- #workflow_core/templates/brief.md

User-selected lenses:

- Add #workflow_core/lenses/domain.md only if the user selects `domain`.
- Add #workflow_core/lenses/change.md only if the user selects `change`.
- Add #workflow_core/lenses/knowledge.md only if the user selects `knowledge`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Keep the default output light. Capture what the user wants, why it matters, what is in and out of scope, and how completion will be recognized.

## Lens Suggestions

- Suggest `domain` when terms or rules are ambiguous. Do not apply it unless selected by the user.
- Suggest `change` when the request should become a tracked change. Do not apply it unless selected by the user.
- Suggest `knowledge` when source material should be preserved. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/briefs/brief_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/brief.md`
- If the request is small and already clear, answer inline and recommend `plan` without forcing a file.

## User Input

{{raw request}}
