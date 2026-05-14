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

Role: {{CONTENT: /workflow_core/roles/designer.md}}
Template: {{CONTENT: /workflow_core/templates/note.md}}

## Copilot Add Context

Required:

- #workflow_core/tasks/explore.md
- #workflow_core/templates/note.md
- relevant source, docs, notes, or code files

User-selected lenses:

- Add #workflow_core/lenses/architecture.md only if the user selects `architecture`.
- Add #workflow_core/lenses/strategy.md only if the user selects `strategy`.
- Add #workflow_core/lenses/knowledge.md only if the user selects `knowledge`.
- Add #workflow_core/lenses/debug.md only if the user selects `debug`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Use this task to learn before deciding. Inspect the relevant code, docs, examples, raw material, or small experiments, then summarize only what helps the next task.

## Lens Suggestions

- Suggest `architecture` when boundaries or dependencies are part of the question. Do not apply it unless selected by the user.
- Suggest `strategy` when the user wants early technical route exploration. Do not apply it unless selected by the user.
- Suggest `knowledge` when preserving source material matters. Do not apply it unless selected by the user.
- Suggest `debug` when exploring a failure or uncertain behavior. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/notes/note_{topic}.md`
- With `strategy` lens, use `workflow_core/templates/options.md` when early route comparison is useful.
- With `knowledge` lens, preserve raw material under `.docs/knowledge/raw/` and update `.docs/knowledge/wiki/`.
- Temporary experiments may use `.sandbox/{topic}/`, but do not modify production code during exploration.

## User Input

{{question, material, code scope, or feasibility concern}}
