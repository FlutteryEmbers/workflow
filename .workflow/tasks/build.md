---
id: build
role: builder
purpose: Apply an explicit workflow-managed plan with bounded execution, environment discipline, verification trace, and reusable discovery capture.
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

`build` is special because it is not a general implementation agent. It is a workflow-aware bounded executor: it applies the explicit plan, limits command/path trial-and-error, records command provenance, produces an execution trace, and surfaces reusable execution discoveries through persist candidates.

## Context Injection

Role: {{CONTENT: /.workflow/roles/builder.md}}

## Mode Rules

- Start with `## Execution Intent` in `Mode: execute`, naming the explicit plan, selected lenses, scope, and key constraints.
- Ask instead of editing when the plan is missing, scope is unclear, not executable enough, or requested edits exceed the plan.
- `Mode: discuss` is default: explain the build approach or missing prerequisites in chat, do not write files.
- `Mode: persist`: not valid for this task; use `persist` for session artifacts or `sync` for project docs / code-adjacent README.
- `Mode: execute`: required for repository changes and must include an explicit `Plan`.
- For native external-agent Implement, use the external-agent path instead of this task, then run `review` on the resulting diff.
- `build` does not require or check `Abstraction Level: implementation-plan`. It only requires an explicit plan that is concrete enough to execute safely.

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
- `Mode: execute` with `Output: compact`: minimal diff inside the plan scope plus compact `Execution Trace`.
- `Mode: execute` with `Output: full`, blocked, partial, failed verification, pitfall found, scope-expansion risk, reusable execution discovery, or user-requested trace persistence: full `Execution Trace`.

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

## Execution Environment Contract

Before running verification commands, establish the execution environment from repo facts instead of guessing:

- `CWD`: current working directory used for the command.
- `Repo Root`: detected repository root.
- `OS / Shell`: operating system and shell when relevant to path or quoting.
- `Package Manager / Runner`: package manager, task runner, Makefile, build tool, test tool, or none.
- `Available Scripts`: relevant scripts discovered from repo manifests or docs.
- `Verification Command Source`: plan, package script, Makefile, project docs, existing CI config, or confirmed repo fact.
- `Retry Budget`: default `2` for command/path/shell/quoting failures unless the explicit plan states a smaller budget.

Do not blindly try multiple directories, shells, path separators, or command variants. If a command fails due to path, cwd, shell, or quoting, use read-only repo inspection to pick one evidence-backed correction. Stop when the retry budget for the same failure class is exhausted and report `Verification Blocked`.

## Command Provenance

Verification commands must have provenance. Prefer, in order:

- explicit command from the plan
- repo manifest script such as `package.json`, `pyproject.toml`, `Cargo.toml`, or equivalent
- `Makefile` or documented project command
- existing CI or project docs
- confirmed repo fact from read-only inspection

Do not invent ad hoc commands when the repo provides a script. If no safe command source exists, mark verification as `not run` or `blocked` and explain what source is missing.

This stop-on-scope-expansion behavior is embedded critique, not the `redteam` lens. Do not perform redteam critique during `build`; stop and route back to critique/review when the plan appears unsafe or under-specified.

If `Mode: execute` or an explicit executable `Plan` is missing, do not modify files; explain what is needed to proceed.

Do not treat native Plan/Implement output as workflow-managed execution unless the user explicitly provides it as the `Plan` for `Mode: execute`.

Do not treat path status as approval. A plan under `.session/threads/**` is executable only when the user invokes `build` with it and it is concrete enough to execute safely.

Do not infer execution authorization from any `Abstraction Level` label. The user invoking `build` with an explicit executable plan is the authorization; the plan still must name scope, target files, allowed changes, verification, and stop conditions.

Do not execute `notes/**`. Exploration notes are disposable working memory and must be converted into a concrete plan under `.session/threads/**` or provided as an explicit executable plan before implementation.

Build may update code, docs, prompts, templates, or workflow artifacts only when the plan explicitly names them. Do not modify `.workflow/**`, `.session/**`, `docs/**`, or `src/**/README.md` unless the plan names those targets.

When writing `docs/**`, the plan must explicitly name the docs targets and include Project Docs conditions: source, scope, sync object, source of truth, target selection, alignment success criteria, existing docs structure, and safety. If these conditions are missing, do not modify `docs/**`; output `Docs Follow-up` and recommend `sync`.

After implementation, output `Docs Follow-up` only when the change clearly affects architecture, public behavior, module responsibility, execution constraints, or agent/human onboarding context. Do not invent docs work for small or temporary changes.

## Reusable Execution Discovery

Capture a reusable execution discovery when build learns a non-obvious fact that can reduce future trial-and-error, such as the correct cwd, OS/shell behavior, command source, frontend route, viewport requirement, slow but reliable test, path convention, generated-file constraint, or recurring failure mode.

Ordinary successful execution does not need persistence. Suggest persistence only when the discovery is reusable, non-obvious, explains repo reality, prevents future command/path churn, or the user asks to save it.

## Execution Trace

Default compact output:

```text
Execution Trace:
- Result: completed | partial | blocked
- Changed: <count and short description>
- Verification: passed | failed | not run | blocked
- Environment: CWD=<path>; Command Source=<plan | script | Makefile | docs | CI | repo fact | none>; Retry Budget=<used>/<limit>
- Pitfalls: none | <count> found, likely <plan gap | repo reality | missing context | test failure | model mistake | unclear>
- Reusable Execution Discovery: none | <short discovery>
- Follow-up: none | persist thread audit note | persist inbox capture | review | plan revision | sync
```

Use full output only when `Output: full`, the build is blocked or partial, verification failed, a pitfall was found, scope expansion risk appeared, or the user asks to persist the trace:

```text
Execution Trace:
- Plan Used: <plan path or inline plan>
- Result: completed | partial | blocked
- Execution Environment Contract:
  - CWD: <path>
  - Repo Root: <path>
  - OS / Shell: <os and shell or unknown>
  - Package Manager / Runner: <tool or none>
  - Available Scripts: <relevant scripts or none>
  - Retry Budget: <used>/<limit>
- Changed Files:
  - <path and reason>
- Completed Steps:
  - <step and evidence>
- Skipped Steps:
  - <step and reason>
- Verification Trace:
  - Plan Step: <step>
    Verification Command: <command or none>
    CWD: <path>
    Command Source: <plan | script | Makefile | docs | CI | repo fact | none>
    Result: <passed | failed | blocked | not run>
    Deviation: <none | deviation from plan>
- Deviations From Plan:
  - <deviation or none>
- Pitfalls Encountered:
  - Pitfall: <what happened>
    Observed During: <step or file>
    Evidence: <file, command, error, or observation>
    Likely Source: plan gap | repo reality | missing context | test failure | model mistake | unclear
    Impact: <effect on execution>
    Follow-up Suggested: persist thread audit note | persist inbox capture | review | plan revision | stable-document sync | none
- Reusable Execution Discoveries:
  - Discovery: <fact or caution worth remembering>
    Future Use: <how future build/review/plan/sync should use it>
    Promotion Candidate: thread note | inbox capture | project docs | code README | build adapter | none
- Suggested Persist Candidate:
  - <none | Artifact=note; Artifact State=working; Intent=audit; Thread=<thread>; Topic=<topic>_execution_trace>
  - <none | Artifact=note; Artifact State=inbox; Intent=capture; Topic=<topic>_execution_discovery>
```

Output `Suggested Persist Candidate` only when there are pitfalls, blocked or partial execution, failed verification, reusable execution discovery, or the user asks to save the execution trace. `build` must not write `.session/**`; use `persist` to store current-work-item audit output as `Artifact: note`, `Intent: audit`, and reusable untriaged discoveries as `Artifact: note`, `Artifact State: inbox`, `Intent: capture`.

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
- Execution traces are output only. Persist them through `persist` as `Artifact: note` and `Intent: audit` when useful for current-work-item audit.
- Reusable execution discoveries are output only. Persist them through `persist` as inbox notes with `Intent: capture`; inbox capture is not source of truth and is not an execution plan.

## User Input

{{explicit plan and build constraints}}
