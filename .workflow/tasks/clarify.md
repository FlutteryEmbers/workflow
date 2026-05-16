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

Role: {{CONTENT: /.workflow/roles/analyst.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial; ask only when ambiguity would affect file writes, execution, or material scope.
- `Mode: discuss` is default: clarify in chat, do not load templates, and do not write files.
- `Mode: persist`: write only the requested `.docs/**` target using one of the persist templates below.
- `Mode: execute`: not valid for this task; use `build` with an approved plan.

## Persist Templates

- Default: `.workflow/templates/brief.md`

## Copilot Add Context

Required:

- #.workflow/tasks/clarify.md
- raw request or relevant source context

For `Mode: persist`:

- Add #.workflow/templates/brief.md
- Provide `Target: .docs/work/briefs/brief_{topic}.md` or a `.docs/changes/{change_id}/brief.md` target when using `change`.

User-selected lenses:

- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Add #.workflow/lenses/change.md only if the user selects `change`.
- Add #.workflow/lenses/knowledge.md only if the user selects `knowledge`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Keep the default output light. Capture what the user wants, why it matters, what is in and out of scope, and how completion will be recognized.

## Lens Suggestions

- Suggest `domain` when terms or rules are ambiguous. Do not apply it unless selected by the user.
- Suggest `change` when the request should become a tracked change. Do not apply it unless selected by the user.
- Suggest `knowledge` when source material should be preserved. Do not apply it unless selected by the user.

## Output Rules

- In `Mode: discuss`, answer inline and recommend the next task without forcing a file.
- Default path: `{WorkflowRoot}/.docs/work/briefs/brief_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/brief.md`

## User Input

{{raw request}}
