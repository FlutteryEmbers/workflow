---
id: build
role: builder
purpose: Apply an approved plan to repository artifacts.
inputs:
  - plan
outputs:
  - repository_changes
  - code_or_documentation_updates
user_selectable_lenses:
  - test
  - debug
  - change
done_check:
  - plan_was_followed
  - verification_evidence_recorded
  - artifact_boundaries_preserved
---

# Build Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/builder.md}}

## Mode Rules

- Start with `## Execution Understanding` in `Mode: execute`, naming the approved plan, selected lenses, scope, and key constraints.
- Ask instead of editing when the approved plan is missing, scope is unclear, or requested edits exceed the plan.
- `Mode: discuss` is default: explain the build approach or missing prerequisites in chat, do not write files.
- `Mode: persist`: not valid for this task; use the relevant documentation task instead.
- `Mode: execute`: required for repository changes and must include an approved `Plan`.

## Copilot Add Context

Required:

- #.workflow/tasks/build.md
- approved plan file for `Mode: execute`
- target source files, docs, prompts, templates, or workflow artifacts

User-selected lenses:

- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Add #.workflow/lenses/change.md only if the user selects `change`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

In `Mode: execute`, implement the approved plan. Read relevant artifacts first, keep edits inside the plan's scope, and stop if the plan requires unapproved interface, config, data, architecture, or workflow behavior changes.

If `Mode: execute` or an approved `Plan` is missing, do not modify files; explain what is needed to proceed.

Build may update code, docs, prompts, templates, or workflow artifacts only when the approved plan explicitly names them. Do not modify `.workflow/**` unless the approved plan targets workflow behavior.

Build may update `src/**/README.md` only when the approved plan explicitly asks for code-adjacent README maintenance. Do not decide during build whether docs or code are wrong; use `review --lens consistency` for that judgment.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.
- Suggest `change` when evidence should be recorded in a tracked change. Do not apply it unless selected by the user.

## Output Rules

- Repository changes live in the project codebase.
- Workflow system changes live in `.workflow/**` and require an explicit approved plan.
- Code-adjacent README updates live in `src/**/README.md` and require an explicit approved plan.
- With `change` lens, record evidence in `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`.

## User Input

{{approved plan and build constraints}}
