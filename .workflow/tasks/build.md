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
- `Mode: persist`: not valid for this task; use `persist` for session artifacts or `sync` for project docs / code-adjacent README.
- `Mode: execute`: required for repository changes and must include an approved `Plan`.
- For native external-agent Implement, use the external-agent path instead of this task, then run `review` on the resulting diff.

## When To Use

- Use only for workflow-managed implementation after an approved plan exists.
- Prefer approved plans from `.session/accepted/plan_{topic}.md`.
- Use when repository artifacts must be changed inside an explicit scope with verification evidence.

## Do Not Use When

- Do not use to create or approve the plan; use `plan` and `review`.
- Do not use for native external-agent implementation; use the external-agent path and then `review`.
- Do not use to persist session artifacts or project docs directly.

## Expected Output

- `Mode: discuss`: missing prerequisite or build-readiness guidance only.
- `Mode: execute`: minimal diff inside the approved scope, changed files, verification evidence, and any stopped scope-expansion reason.

## Task Boundary Check

Before building, classify the request:

- `fits`: `Mode: execute` includes an approved plan and requested edits stay inside that plan.
- `fits_with_preflight`: in `Mode: discuss`, user asks whether a plan is ready to build; run conditional implicit preflight for approved-plan readiness only.
- `missing_prerequisite`: approved `Plan` is missing, unclear, not approved, or points only to `.session/drafts/**` without explicit approval.
- `missing_prerequisite`: approved `Plan` points to `notes/**`; disposable exploration notes are not approved execution sources.
- `composite`: user asks to implement from target docs/current code without an approved plan; recommend `plan -> review -> external-agent/build -> review`.
- `wrong_task`: user asks to persist session artifacts; recommend `persist`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

Conditional implicit preflight for `build` is allowed only in `Mode: discuss` and only checks readiness: approved plan, scope, allowed changes, do-not-touch areas, and verification. In `Mode: execute`, do not preflight; if the approved plan is missing or unclear, block without editing.

If not `fits`, do not modify files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/build.md
- approved plan file, preferably `.session/accepted/plan_{topic}.md`, for `Mode: execute`
- target source files, docs, prompts, templates, or workflow artifacts

User-selected lenses:

- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

In `Mode: execute`, implement the approved plan. Read relevant artifacts first, keep edits inside the plan's scope, and stop if the plan requires unapproved interface, config, data, architecture, documentation, or workflow behavior changes.

Before editing, check the approved plan's compatibility and constraint policy:

- If the plan does not state otherwise, assume `Compatibility: preserve` and `Constraint Mode: respect`.
- Execute breaking changes only when the approved plan explicitly states `Compatibility: breaking` and names the removed compatibility.
- Execute constraint overrides only when the approved plan explicitly states `Constraint Mode: propose_override` or `Constraint Mode: prototype_exception` and names the exception scope.
- Treat unapproved legacy entry removal, alias removal, migration removal, fallback removal, or constraint bypass as scope expansion.
- If breaking/constraint details are missing, stop and route back to `plan` or `review`; do not infer them during build.

Use minimal diff discipline: do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites. After each major plan step, record the verification evidence requested by the plan. If implementation reveals that scope must expand, stop and return to `plan` or `review` instead of editing beyond the approved scope.

This stop-on-scope-expansion behavior is built-in safety, not the `redteam` lens. Do not perform redteam critique during `build`; route back to `review --lens redteam` when the approved plan appears unsafe or under-specified.

If `Mode: execute` or an approved `Plan` is missing, do not modify files; explain what is needed to proceed.

Do not treat native Plan/Implement output as workflow-managed execution unless the user explicitly provides it as the approved `Plan` for `Mode: execute`.

Do not execute `.session/drafts/**` by default. Draft plans are intermediate working memory; they must be promoted to `.session/accepted/plan_{topic}.md` or explicitly approved by the user, ideally after `review`.

Do not execute `notes/**`. Exploration notes are disposable working memory and must be converted into an approved plan under `.session/accepted/**` or explicitly approved through a reviewed external-agent plan before implementation.

Build may update code, docs, prompts, templates, or workflow artifacts only when the approved plan explicitly names them. Do not modify `.workflow/**`, `.session/**`, `docs/**`, or `src/**/README.md` unless the approved plan names those targets.

When writing `docs/**`, the approved plan must explicitly name the docs targets and include Project Docs conditions: source, future use, source of truth, future-use success criteria, existing docs structure, and safety. If these conditions are missing, do not modify `docs/**`; output `Docs Follow-up` and recommend `sync`.

After implementation, output `Docs Follow-up` only when the change clearly affects architecture, public behavior, module responsibility, execution constraints, or agent/human onboarding context. Do not invent docs work for small or temporary changes.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.

## Output Rules

- Repository changes live in the project codebase.
- Workflow system changes live in `.workflow/**` and require an explicit approved plan.
- Session working memory lives in `.session/**` and should be updated by `persist`, not `build`.
- `.session/accepted/**` is the preferred session-level source for approved build input.
- `.session/drafts/**` is not executable by default.
- `notes/**` is disposable exploration memory and is not executable by default.
- Code-adjacent README updates live in `src/**/README.md` and require an explicit approved plan.
- Project docs live in `docs/**` and require approved-plan Project Docs conditions; otherwise output `Docs Follow-up` and route to `sync`.
- Compatibility removal and constraint exceptions require explicit approved-plan policy; otherwise stop as unapproved scope expansion.

## User Input

{{approved plan and build constraints}}
