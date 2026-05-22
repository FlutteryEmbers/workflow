---
id: explore
role: designer
purpose: Understand code, materials, current behavior, feasibility, or reference structure.
inputs:
  - question_or_source
outputs:
  - .session/notes/note_{topic}.md
user_selectable_lenses:
  - distill
  - strategy
  - architecture
  - debug
  - language
  - domain
done_check:
  - evidence_is_named
  - unknowns_are_listed
  - next_decision_is_clear
---

# Explore Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default: explain findings in chat, do not load templates, and do not write files.
- `Mode: persist`: write only the requested `.session/notes/**` target.
- `Mode: execute`: not valid for this task.

## Task Boundary Check

Before exploring, classify obvious boundary problems:

- `fits`: user asks to understand code, docs, behavior, feasibility, or reference material.
- `wrong_task`: user asks to choose a direction; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to judge a target or diff; recommend `review`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.
- `missing_prerequisite`: `Mode: persist` lacks a `.session/notes/**` target.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Persist Templates

- Default: `.workflow/templates/note.md`
- With `distill`: `.workflow/templates/distillation.md`
- With `strategy`: `.workflow/templates/options.md`

## Copilot Add Context

Required:

- #.workflow/tasks/explore.md
- relevant source files, docs, references, or `.session/**` context

For `Mode: persist`:

- Add #.workflow/templates/note.md, or #.workflow/templates/distillation.md / #.workflow/templates/options.md when selected.
- Provide `Target: .session/notes/note_{topic}.md`.

User-selected lenses:

- Add #.workflow/lenses/distill.md only if the user selects `distill`.
- Add #.workflow/lenses/strategy.md only if the user selects `strategy`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/domain.md only if the user selects `domain`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Explore enough to reduce uncertainty for the next decision. Separate observed facts from assumptions. Preserve reference-derived structure as session notes first; stable formal documentation should later be handled by `sync`.

## Output Rules

- Default persisted path: `{WorkflowRoot}/.session/notes/note_{topic}.md`.
- With `distill`, write distillation notes to `.session/notes/**` unless the user requests a session decision.
- Do not write `docs/**`; use `sync` after a decision is stable.

## User Input

{{question, source material, code area, or reference to explore}}
