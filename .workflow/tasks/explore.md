---
id: explore
role: designer
purpose: Understand code, materials, current behavior, or feasibility before shaping a solution.
inputs:
  - question_or_scope
outputs:
  - .docs/work/notes/note_{topic}.md
  - .docs/knowledge/raw/{source_name}
  - .docs/knowledge/wiki/{page}.md
user_selectable_lenses:
  - distill
  - architecture
  - strategy
  - knowledge
  - debug
done_check:
  - evidence_is_cited
  - unknowns_are_named
  - next_step_is_clear
---

# Explore Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial; ask only when ambiguity would affect file writes, execution, or material scope.
- `Mode: discuss` is default: explain findings in chat, do not load templates, and do not write files.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize lens views in the user's lens order, then summarize findings and next steps.
- `Mode: persist`: write only the requested `.docs/**` target using one of the persist templates below.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens; split into multiple artifacts if more views are needed.
- `Mode: execute`: not valid for this task; use `build` with an approved plan.

## Persist Templates

- Default: `.workflow/templates/note.md`
- With `distill`: `.workflow/templates/distillation.md`
- With `strategy`: `.workflow/templates/options.md`

## Copilot Add Context

Required:

- #.workflow/tasks/explore.md
- relevant source, docs, notes, or code files

For `Mode: persist`:

- Add #.workflow/templates/note.md, or #.workflow/templates/distillation.md / #.workflow/templates/options.md when the selected lens requires it.
- Provide a `.docs/**` `Target`.

User-selected lenses:

- Add #.workflow/lenses/distill.md only if the user selects `distill`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Add #.workflow/lenses/strategy.md only if the user selects `strategy`.
- Add #.workflow/lenses/knowledge.md only if the user selects `knowledge`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Use this task to learn before deciding. Inspect the relevant code, docs, examples, raw material, or small experiments, then summarize only what helps the next task.

## Lens Suggestions

- Suggest `distill` when studying a strong reference document, architecture folder, RFC, ADR, handbook, or knowledge base for reusable structure. Do not apply it unless selected by the user.
- Suggest `architecture` when boundaries or dependencies are part of the question. Do not apply it unless selected by the user.
- Suggest `strategy` when the user wants early technical route exploration. Do not apply it unless selected by the user.
- Suggest `knowledge` when preserving source material matters. Do not apply it unless selected by the user.
- Suggest `debug` when exploring a failure or uncertain behavior. Do not apply it unless selected by the user.

## Output Rules

- In `Mode: discuss`, summarize findings, unknowns, and next steps in chat only.
- Default path: `{WorkflowRoot}/.docs/work/notes/note_{topic}.md`
- With `distill` lens, use `.workflow/templates/distillation.md` and write `{WorkflowRoot}/.docs/work/notes/note_{source}_distillation.md`.
- With `strategy` lens, use `.workflow/templates/options.md` when early route comparison is useful.
- With `knowledge` lens, preserve raw material under `.docs/knowledge/raw/` and update `.docs/knowledge/wiki/`.
- Temporary experiments may use `.sandbox/{topic}/`, but do not modify production code during exploration.

## User Input

{{question, material, code scope, or feasibility concern}}
