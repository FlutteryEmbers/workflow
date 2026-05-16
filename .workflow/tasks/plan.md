---
id: plan
role: designer
purpose: Convert a clear goal, target design, or solution shape into executable repo-aware steps.
inputs:
  - brief_or_shape
  - target_design_or_concept
  - repo_context_or_notes
outputs:
  - .docs/work/plans/plan_{topic}.md
  - .docs/changes/{change_id}/plan.md
user_selectable_lenses:
  - iteration
  - expand
  - language
  - strategy
  - test
  - architecture
  - change
done_check:
  - target_design_is_linked
  - current_repo_fit_is_named
  - steps_are_executable
  - verification_is_named
  - scope_boundaries_are_preserved
---

# Plan Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}
Template: {{CONTENT: /.workflow/templates/plan.md}}

## Copilot Add Context

Required:

- #.workflow/tasks/plan.md
- #.workflow/templates/plan.md
- relevant brief, shape, concept, note, review, target design, or requested work
- relevant source files or docs when the plan must map onto the current repo

User-selected lenses:

- Add #.workflow/lenses/iteration.md only if the user selects `iteration`.
- Add #.workflow/lenses/expand.md only if the user selects `expand`.
- Add #.workflow/lenses/language.md only if the user selects `language`.
- Add #.workflow/lenses/strategy.md only if the user selects `strategy`.
- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/architecture.md only if the user selects `architecture`.
- Add #.workflow/lenses/change.md only if the user selects `change`.
- Add #.workflow/templates/expanded.md only when the user selects `expand`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest repo-aware plan that another agent or engineer can execute without making product decisions. Start from the target design or chosen direction, map it to the current repository reality, then recommend an executable sequence.

## Lens Suggestions

- Suggest `iteration` when plan steps are still being discussed, reordered, scoped, or revised. Do not apply it unless selected by the user.
- Suggest `expand` when a plan needs detailed steps, examples, pseudocode, validation notes, handoff detail, or split part files. Do not apply it unless selected by the user.
- Suggest `language` when a plan needs full English, translation, terminology consistency, or glossary-ready terms. Do not apply it unless selected by the user.
- Suggest `strategy` when multiple implementation sequences, slicing approaches, or migration paths are viable. Do not apply it unless selected by the user.
- Suggest `test` when verification needs stronger structure. Do not apply it unless selected by the user.
- Suggest `architecture` when the plan must preserve boundaries, interfaces, dependency direction, or architectural constraints. Do not apply it unless selected by the user.
- Suggest `change` when the plan belongs to a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/plans/plan_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/plan.md`
- For target design planning, explicitly connect target design, current repo fit, impact map, step options, and recommended sequence.
- With `expand` lens, use `.workflow/templates/expanded.md` and write `{base}.expanded.md` plus optional `{base}.part_{topic}.md` in the same directory as the source plan.
- Prefer a short executable plan over a comprehensive document.

## User Input

{{brief, shape, concept, repo context, review finding, or requested work}}
