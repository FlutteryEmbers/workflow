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
optional_lenses:
  - architecture
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

## Instructions

Use this task to learn before deciding. Inspect the relevant code, docs, examples, raw material, or small experiments, then summarize only what helps the next task.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/architecture.md}} when boundaries or dependencies are part of the question.
- Use {{CONTENT: /workflow_core/lenses/knowledge.md}} when preserving source material matters.
- Use {{CONTENT: /workflow_core/lenses/debug.md}} when exploring a failure or uncertain behavior.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/notes/note_{topic}.md`
- With `knowledge` lens, preserve raw material under `.docs/knowledge/raw/` and update `.docs/knowledge/wiki/`.
- Temporary experiments may use `.sandbox/{topic}/`, but do not modify production code during exploration.

## User Input

{{question, material, code scope, or feasibility concern}}
