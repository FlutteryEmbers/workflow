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

`build` is the workflow-managed execution task. Native Plan -> Implement from Codex, Copilot, OpenCode, or similar agents is the external-agent write path; it does not use `build`; plan review before implementation and diff review afterward are recommended risk controls.

`build` is special because it is not a general implementation agent. It is a workflow-aware bounded executor: it applies the explicit plan, limits command/path trial-and-error, records command provenance, produces an execution trace, and surfaces reusable execution discoveries through persist candidates.

## Context Injection

Role: {{CONTENT: /.workflow/roles/builder.md}}

## Mode Rules

- Start with `## Execution Intent` in `Mode: execute`, naming the explicit plan, selected lenses, scope, key constraints, `Review Status`, and `Risk Notice`.
- Ask instead of editing when the plan is missing, scope is unclear, not executable enough, or requested edits exceed the plan.
- `Mode: discuss` is default: explain the build approach or missing prerequisites in chat, do not write files.
- `Mode: persist`: not valid for this task; use `persist` for session artifacts or `sync` for project docs / code-adjacent README.
- `Mode: execute`: required for repository changes and must include an explicit `Plan`.
- For native external-agent Implement, use the external-agent path instead of this task, then run `review` on the resulting diff.
- `build` does not require or check a plan kind label. It only requires an explicit plan that is concrete enough to execute safely.

## When To Use

- Use only for workflow-managed implementation when the user explicitly invokes build with a concrete plan.
- Use plans from `.session/threads/{thread}/plan_{topic}.md`, project docs, or an explicit user-provided plan.
- Use when repository artifacts must be changed inside an explicit scope with verification evidence.

## Do Not Use When

- Do not use to create or approve the plan; use `plan` and `review`.
- Do not use for native external-agent implementation; use the external-agent path and then `review`.
- Do not use to persist session artifacts or project docs directly.

## Boundary Layers

- `Core Responsibility`: apply an explicit executable workflow-managed plan with bounded execution, verification evidence, and execution trace.
- `Adjacent Allowance`: report executability gaps, missing prerequisites, pitfalls, reusable execution discoveries, and recommended review or persist follow-up.
- `Forbidden Authority`: do not create or approve the plan, expand scope, infer authorization from input sufficiency, adjacent discussion output, review suggestions, plan path status, or any label, sync stable documents, persist session artifacts, or execute without `Mode: execute` and an explicit executable `Plan`.

Adjacent discussion output from `clarify`, `explore`, `distill`, `shape`, `review`, or `plan` does not grant build authority. `build` may modify repository artifacts only in `Mode: execute`, only with an explicit executable `Plan`, and only inside that plan's scope.

## Response Contract

- `Mode: discuss`: use the shared response groups for missing prerequisites or executability guidance only.
- `Mode: execute`: apply the minimal diff inside plan scope and return one state-driven `Execution Trace`.
- A completed execution without exceptional conditions uses the concise trace fields. Blocked or partial execution, failed verification, pitfalls, scope-expansion risk, reusable discoveries, or a request to persist the trace automatically adds the expanded evidence fields.

## Task Boundary Check

Before building, classify the request:

- `fits`: `Mode: execute` includes an explicit plan and requested edits stay inside that plan.
- `fits_with_preflight`: in `Mode: discuss`, user asks whether a plan is executable; run conditional implicit preflight for execution prerequisites only.
- `missing_prerequisite`: `Plan` is missing, unclear, or not executable enough.
- `missing_prerequisite`: `Plan` points to `notes/**`; disposable exploration notes are not executable sources.
- `composite`: user asks to implement from target docs/current code without a concrete plan or confirmed source-of-truth verdict; recommend `review -> plan -> review -> external-agent/build -> review`.
- `wrong_task`: user asks to persist session artifacts; recommend `persist`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

Conditional implicit preflight for `build` is allowed only in `Mode: discuss` and only checks executability: plan, scope, target files or target areas, allowed changes, do-not-touch areas, step verification, stop conditions, and compatibility / constraint policy. In `Mode: execute`, do not run implicit preflight; instead perform execution plan validation. If the plan is missing, unclear, or not executable enough, block without editing.

Boundary handling:

- `fits`: validate the explicit plan, then execute only inside plan scope.
- `fits_with_preflight`: in `Mode: discuss`, run execution-prerequisite preflight and report executability only.
- `composite`: output the recommended segmented path; do not modify files.
- `wrong_task` or `missing_prerequisite`: stop and return Boundary, Reason, Recommended Path, and Next Prompt.

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

In `Mode: execute`, validate and implement the explicit plan. The user invoking `build` is the execution authorization; the task's job is to enforce plan scope and executability. Read relevant artifacts first, keep edits inside the plan's scope, and stop if the plan requires unplanned interface, config, data, architecture, documentation, or workflow behavior changes.

`build` records execution facts, not review verdicts. It may report pitfalls and likely sources observed during execution, but it must not decide whether the plan was correct, whether the diff is acceptable, or whether docs should change. Route those judgments to `review`.

Missing review is not by itself a build blocker. Report `Review Status: reviewed | not reviewed | unknown` and a `Risk Notice` instead. Use `Risk Notice: review recommended` for material uncertainty or medium-risk plans. Use `Risk Notice: review strongly recommended` for breaking changes, constraint overrides, public API, data, security, source-of-truth, stable docs projection, multi-surface plans, high reversal cost, or ambiguous verification. Continue execution only when the explicit plan is executable and the user invoked `Mode: execute`.

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

Do not infer execution authorization from input sufficiency, adjacent discussion output, review suggestions, plan path status, or any plan label. The user invoking `build` with an explicit executable plan is the authorization; the plan still must name scope, target files or target areas, allowed changes, verification, and stop conditions.

Do not execute `notes/**`. Exploration notes are disposable working memory and must be converted into a concrete plan under `.session/threads/**` or provided as an explicit executable plan before implementation.

Build may update code, prompts, templates, workflow artifacts, or explicitly planned docs edits only when the plan names them. Do not modify `.workflow/**`, `.session/**`, `docs/**`, or `src/**/README.md` unless the plan names those targets.

By default, build does not perform stable-document projection. When writing `docs/**` or `src/**/README.md`, the plan must explicitly name concrete docs edits and include Project Docs conditions: source, scope, sync object, source of truth, target selection, alignment success criteria, existing docs structure, and safety. If these conditions are missing, do not modify `docs/**` or `src/**/README.md`; output `Docs Follow-up` and recommend `sync`.

After implementation, output `Docs Follow-up` only when the change clearly affects architecture, public behavior, module responsibility, execution constraints, or agent/human onboarding context. Do not invent docs work for small or temporary changes.

## Reusable Execution Discovery

Capture a reusable execution discovery when build learns a non-obvious fact that can reduce future trial-and-error, such as the correct cwd, OS/shell behavior, command source, frontend route, viewport requirement, slow but reliable test, path convention, generated-file constraint, or recurring failure mode.

Ordinary successful execution does not need persistence. Suggest persistence only when the discovery is reusable, non-obvious, explains repo reality, prevents future command/path churn, or the user asks to save it.

## Execution Trace

Use the shared response groups in both discuss and execute modes. In execute
mode, keep the task-specific trace fields inside those groups:

```text
User Intent
- <the explicit plan and requested execution outcome>
Task State
- Boundary Advice: <only for discuss, blocked, wrong-task, or missing-prerequisite responses>
- Result: <completed|partial|blocked>
- Review Status: <reviewed|not reviewed|unknown>
- Risk Notice: <none|review recommended|review strongly recommended>
Primary Result
- Execution Trace:
  - Plan Used: <plan path or inline plan>
  - Changed: <count and short description>
  - Verification: <passed|failed|not run|blocked>
Supporting Information
- Environment: <CWD, command source, retry budget>
- Pitfalls: <none or count and likely source>
- Reusable Execution Discovery: <none or short discovery>
- Execution Environment Contract: <expanded only when exceptional conditions require it>
- Changed Files: <expanded path and reason list when needed>
- Completed Steps: <expanded step and evidence list when needed>
- Skipped Steps: <expanded step and reason list when needed>
- Verification Trace: <expanded command, CWD, provenance, result, and deviation per plan step when needed>
- Deviations From Plan: <expanded when non-empty>
- Pitfalls Encountered: <expanded observation, evidence, source, impact, and follow-up when needed>
- Reusable Execution Discoveries: <expanded fact, future use, and promotion candidate when needed>
Next
- Docs Follow-up: <only when the change clearly affects durable docs concerns>
- Follow-up: <none|persist thread audit note|persist inbox capture|review|plan revision|sync>
Persistence
- Suggested Persist Candidate: <audit note or inbox capture; only when its trigger applies>
```

Use the concise fields for ordinary successful execution. Add the expanded
supporting fields only when execution is blocked or partial, verification fails,
a pitfall or scope-expansion risk appears, a reusable discovery is found, or the
user asks to persist the trace.

`Suggested Persist Candidate` is allowed only under those same conditions.
`build` never writes `.session/**`; persist current-work-item audit output as
`Artifact: note`, `Intent: audit`, and reusable untriaged discoveries as
`Artifact: note`, `Artifact State: inbox`, `Intent: capture`.

## Lens Suggestions

- Suggest `test` when behavior changes need explicit verification. Do not apply it unless selected by the user.
- Suggest `debug` when implementation depends on diagnosing a failure. Do not apply it unless selected by the user.

## Repository Boundary Rules

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
