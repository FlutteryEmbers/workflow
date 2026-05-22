---
id: explore
role: designer
purpose: Understand code, materials, current behavior, feasibility, or reference structure.
inputs:
  - question_or_source
outputs:
  - .session/inbox/note_{topic}.md
  - .session/drafts/option_{topic}.md
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
- `Mode: persist`: write only the requested `.session/inbox/**` target, or `.session/drafts/option_{topic}.md` when the user asks for structured candidate options.
- `Mode: execute`: not valid for this task.

## When To Use

- Use when the user needs evidence about current code, docs, behavior, feasibility, reference material, or unknowns.
- Use before `shape` or `plan` when the decision depends on repository reality or external references.

## Do Not Use When

- Do not use to make the final direction decision; use `shape`.
- Do not use to judge whether a plan, diff, or implementation is acceptable; use `review`.
- Do not use to create executable steps; use `plan`.
- Do not use to update formal docs; use `sync`.

## Expected Output

- `Mode: discuss`: facts, evidence, assumptions, unknowns, and suggested next decision.
- `Mode: persist`: a `.session/inbox/**` note that separates observed facts from assumptions, or a `.session/drafts/option_{topic}.md` candidate set when exploration becomes structured alternatives.

## Task Boundary Check

Before exploring, classify obvious boundary problems:

- `fits`: user asks to understand code, docs, behavior, feasibility, or reference material.
- `fits_with_preflight`: request scope is too broad, source is unclear, or the request may actually belong to `review`, `shape`, or `plan`. In `Mode: discuss`, run conditional boundary preflight only.
- `wrong_task`: user asks to choose a direction; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to judge a target or diff; recommend `review`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.
- `missing_prerequisite`: `Mode: persist` lacks a `.session/inbox/**` target or a structured `.session/drafts/option_{topic}.md` target.

Conditional implicit preflight for `explore` only checks boundary, source, and scope. Do not duplicate exploration inside preflight; once the boundary is clear, proceed with normal explore or recommend the right task.

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
- Provide `Target: .session/inbox/note_{topic}.md` by default.
- Provide `Target: .session/drafts/option_{topic}.md` only when the output is structured candidate options.

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

- Default persisted path: `{WorkflowRoot}/.session/inbox/note_{topic}.md`.
- With `distill`, write distillation notes to `.session/inbox/**` unless the user requests a session decision.
- With structured alternatives, write candidate options to `{WorkflowRoot}/.session/drafts/option_{topic}.md`; drafts are not approved sources for execution.
- Do not write `docs/**`; use `sync` after a decision is stable.

## User Input

{{question, source material, code area, or reference to explore}}
