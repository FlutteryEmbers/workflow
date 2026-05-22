---
id: build
role: builder
purpose: Apply an approved workflow-managed plan to repository artifacts.
inputs:
  - plan
outputs:
  - repository_changes
  - code_or_documentation_updates
user_selectable_lenses:
  - test
  - debug
done_check:
  - plan_was_followed
  - verification_evidence_recorded
  - artifact_boundaries_preserved
---

# Build Task

`build` is the workflow-managed execution task. Native Plan -> Implement from Codex, Copilot, OpenCode, or similar agents is the external-agent write path; it does not use `build`, but its plan should be audited before implementation and its diff should be reviewed afterward.

## Context Injection

Role: {{CONTENT: /.workflow/roles/builder.md}}

## Mode Rules

- Start with `## Execution Understanding` in `Mode: execute`, naming the approved plan, selected lenses, scope, and key constraints.
- Ask instead of editing when the approved plan is missing, scope is unclear, or requested edits exceed the plan.
- `Mode: discuss` is default: explain the build approach or missing prerequisites in chat, do not write files.
- `Mode: persist`: not valid for this task; use the relevant session or sync task instead.
- `Mode: execute`: required for repository changes and must include an approved `Plan`.
- For native external-agent Implement, use the external-agent path instead of this task, then run `review` on the resulting diff.

## Task Boundary Check

Before building, classify the request:

- `fits`: `Mode: execute` includes an approved plan and requested edits stay inside that plan.
- `fits_with_preflight`: in `Mode: discuss`, user asks whether a plan is ready to build; perform read-only plan-readiness preflight.
- `missing_prerequisite`: approved `Plan` is missing, unclear, or not approved.
- `composite`: user asks to implement from target docs/current code without an approved plan; recommend `plan -> review -> external-agent/build -> review`.
- `wrong_task`: user asks to save session notes, decisions, or formal docs; recommend the corresponding persist task or `sync`.

If not `fits`, do not modify files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/build.md
- approved plan file or approved session decision for `Mode: execute`
- target source files, docs, prompts, templates, or workflow artifacts

User-selected lenses:

- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

In `Mode: execute`, implement the approved plan. Read relevant artifacts first, keep edits inside the plan's scope, and stop if the plan requires unapproved interface, config, data, architecture, documentation, or workflow behavior changes.

If `Mode: execute` or an approved `Plan` is missing, do not modify files; explain what is needed to proceed.

Do not treat native Plan/Implement output as workflow-managed execution unless the user explicitly provides it as the approved `Plan` for `Mode: execute`.

Build may update code, docs, prompts, templates, or workflow artifacts only when the approved plan explicitly names them. Do not modify `.workflow/**`, `.session/**`, `docs/**`, or `src/**/README.md` unless the approved plan names those targets.

When writing `docs/**`, obey Formal Docs Rules from `sync`.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.

## Output Rules

- Repository changes live in the project codebase.
- Workflow system changes live in `.workflow/**` and require an explicit approved plan.
- Session working memory lives in `.session/**` and should usually be updated by session tasks, not `build`.
- Code-adjacent README updates live in `src/**/README.md` and require an explicit approved plan.
- Formal docs live in `docs/**` and require Formal Docs Rules.

## User Input

{{approved plan and build constraints}}
