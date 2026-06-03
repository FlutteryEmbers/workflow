---
id: build
role: builder
purpose: Apply an explicit workflow-managed plan to repository artifacts when the user invokes build.
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

- Start with `## Execution Understanding` in `Mode: execute`, naming the explicit plan, selected lenses, scope, and key constraints.
- Ask instead of editing when the plan is missing, scope is unclear, not executable enough, or requested edits exceed the plan.
- `Mode: discuss` is default: explain the build approach or missing prerequisites in chat, do not write files.
- `Mode: persist`: not valid for this task; use `persist` for session artifacts or `sync` for project docs / code-adjacent README.
- `Mode: execute`: required for repository changes and must include an explicit `Plan`.
- For native external-agent Implement, use the external-agent path instead of this task, then run `review` on the resulting diff.
- `build` does not require or check `Planning Level: implementation-plan`. It only requires an explicit plan that is concrete enough to execute safely.

## When To Use

- Use only for workflow-managed implementation when the user explicitly invokes build with a concrete plan.
- Use plans from `.session/threads/{thread}/plan_{topic}.md`, project docs, or an explicit user-provided plan.
- Use when repository artifacts must be changed inside an explicit scope with verification evidence.

## Do Not Use When

- Do not use to create or approve the plan; use `plan` and `review`.
- Do not use for native external-agent implementation; use the external-agent path and then `review`.
- Do not use to persist session artifacts or project docs directly.

## Expected Output

- `Mode: discuss`: missing prerequisite or build-readiness guidance only.
- `Mode: execute` with `Output: compact`: minimal diff inside the plan scope plus compact `Execution Summary`.
- `Mode: execute` with `Output: full`, blocked, partial, failed verification, pitfall found, scope-expansion risk, or user-requested summary persistence: full `Execution Summary`.

## Task Boundary Check

Before building, classify the request:

- `fits`: `Mode: execute` includes an explicit plan and requested edits stay inside that plan.
- `fits_with_preflight`: in `Mode: discuss`, user asks whether a plan is ready to build; run conditional implicit preflight for plan readiness only.
- `missing_prerequisite`: `Plan` is missing, unclear, or not executable enough.
- `missing_prerequisite`: `Plan` points to `notes/**`; disposable exploration notes are not executable sources.
- `composite`: user asks to implement from target docs/current code without a concrete plan; recommend `plan -> review -> external-agent/build -> review`.
- `wrong_task`: user asks to persist session artifacts; recommend `persist`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

Conditional implicit preflight for `build` is allowed only in `Mode: discuss` and only checks readiness: plan, scope, target files, allowed changes, do-not-touch areas, step verification, stop conditions, and compatibility / constraint policy. In `Mode: execute`, do not preflight; if the plan is missing or unclear, block without editing.

If not `fits`, do not modify files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/build.md
- explicit plan file, usually `.session/threads/{thread}/plan_{topic}.md`, for `Mode: execute`
- target source files, docs, prompts, templates, or workflow artifacts

User-selected lenses:

- Add #.workflow/lenses/test.md only if the user selects `test`.
- Add #.workflow/lenses/debug.md only if the user selects `debug`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

In `Mode: execute`, implement the explicit plan. The user invoking `build` is the execution authorization; the task's job is to enforce plan scope and executability. Read relevant artifacts first, keep edits inside the plan's scope, and stop if the plan requires unplanned interface, config, data, architecture, documentation, or workflow behavior changes.

`build` records execution facts, not review verdicts. It may report pitfalls and likely sources observed during execution, but it must not decide whether the plan was correct, whether the diff is acceptable, or whether docs should change. Route those judgments to `review`.

Before editing, check the plan's compatibility and constraint policy:

- If the plan does not state otherwise, assume `Compatibility: preserve` and `Constraint Mode: respect`.
- Execute breaking changes only when the plan explicitly states `Compatibility: breaking` and names the removed compatibility.
- Execute constraint overrides only when the plan explicitly states `Constraint Mode: propose_override` or `Constraint Mode: prototype_exception` and names the exception scope.
- Treat unplanned legacy entry removal, alias removal, migration removal, fallback removal, or constraint bypass as scope expansion.
- If breaking/constraint details are missing, stop and route back to `plan` or `review`; do not infer them during build.

Use minimal diff discipline: do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites. After each major plan step, record the verification evidence requested by the plan. If implementation reveals that scope must expand, stop and return to `plan` or `review` instead of editing beyond the plan scope.

This stop-on-scope-expansion behavior is built-in safety, not the `redteam` lens. Do not perform redteam critique during `build`; route back to `review --lens redteam` when the plan appears unsafe or under-specified.

If `Mode: execute` or an explicit executable `Plan` is missing, do not modify files; explain what is needed to proceed.

Do not treat native Plan/Implement output as workflow-managed execution unless the user explicitly provides it as the `Plan` for `Mode: execute`.

Do not treat path status as approval. A plan under `.session/threads/**` is executable only when the user invokes `build` with it and it is concrete enough to execute safely.

Do not infer execution authorization from the `conceptual` lens or from any `Planning Level` label. The user invoking `build` with an explicit plan is the authorization; the plan still must name scope, target files, allowed changes, verification, and stop conditions.

Do not execute `notes/**`. Exploration notes are disposable working memory and must be converted into a concrete plan under `.session/threads/**` or provided as an explicit executable plan before implementation.

Build may update code, docs, prompts, templates, or workflow artifacts only when the plan explicitly names them. Do not modify `.workflow/**`, `.session/**`, `docs/**`, or `src/**/README.md` unless the plan names those targets.

When writing `docs/**`, the plan must explicitly name the docs targets and include Project Docs conditions: source, future use, source of truth, future-use success criteria, existing docs structure, and safety. If these conditions are missing, do not modify `docs/**`; output `Docs Follow-up` and recommend `sync`.

After implementation, output `Docs Follow-up` only when the change clearly affects architecture, public behavior, module responsibility, execution constraints, or agent/human onboarding context. Do not invent docs work for small or temporary changes.

## Execution Summary

Default compact output:

```text
Execution Summary:
- Result: completed | partial | blocked
- Changed: <count and short description>
- Verification: passed | failed | not run
- Pitfalls: none | <count> found, likely <plan gap | repo reality | missing context | test failure | model mistake | unclear>
- Follow-up: none | persist note | review | plan revision | sync
```

Use full output only when `Output: full`, the build is blocked or partial, verification failed, a pitfall was found, scope expansion risk appeared, or the user asks to persist the summary:

```text
Execution Summary:
- Plan Used: <plan path or inline plan>
- Result: completed | partial | blocked
- Changed Files:
  - <path and reason>
- Completed Steps:
  - <step and evidence>
- Skipped Steps:
  - <step and reason>
- Verification:
  - <verification and result>
- Deviations From Plan:
  - <deviation or none>
- Pitfalls Encountered:
  - Pitfall: <what happened>
    Observed During: <step or file>
    Evidence: <file, command, error, or observation>
    Likely Source: plan gap | repo reality | missing context | test failure | model mistake | unclear
    Impact: <effect on execution>
    Follow-up Suggested: persist note | review | plan revision | docs sync | none
- Useful Notes For Future Work:
  - <fact or caution worth remembering>
- Suggested Persist Hint: Artifact=note; Intent=audit; Thread=<thread>; Topic=<topic>_execution_summary
```

Output `Suggested Persist Hint` only when there are pitfalls, blocked or partial execution, failed verification, or the user asks to save the execution summary. `build` must not write `.session/**`; use `persist` to store the summary as `Artifact: note` and `Intent: audit`.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.

## Output Rules

- Repository changes live in the project codebase.
- Workflow system changes live in `.workflow/**` and require an explicit plan.
- Session working memory lives in `.session/**` and should be updated by `persist`, not `build`.
- `.session/threads/**` is session working memory and may contain executable plans when the user explicitly invokes `build` with a concrete plan.
- `notes/**` is disposable exploration memory and is not executable by default.
- Code-adjacent README updates live in `src/**/README.md` and require an explicit plan.
- Project docs live in `docs/**` and require plan-level Project Docs conditions; otherwise output `Docs Follow-up` and route to `sync`.
- Compatibility removal and constraint exceptions require explicit plan policy; otherwise stop as scope expansion.
- Execution summaries are output only. Persist them through `persist` as `Artifact: note` and `Intent: audit` when useful.

## User Input

{{explicit plan and build constraints}}
