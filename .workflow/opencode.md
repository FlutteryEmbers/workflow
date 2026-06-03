# OpenCode Adapter

OpenCode support is a thin adapter. It does not replace `.workflow/tasks/**`, `.workflow/lenses/**`, or `.workflow/templates/**`, and it does not create a separate OpenCode workflow.

Use OpenCode when it helps with context reading, plan drafting, or bounded implementation. Keep `.workflow/**` as the source of truth for task semantics, lenses, write boundaries, and Project Docs Rules.

OpenCode may suggest `redteam` when risk triggers match, but must not auto-load or apply it. Use full `redteam` only when the user explicitly selected that lens or the prompt explicitly includes it.

Use `conceptual` only when the user explicitly wants concept-first planning, high-level planning, low-level implementation planning, or strong-model-to-weak-model handoff. It is not loaded by default.

When unsure, start with `shape`. Use `explore` for evidence and `review` for verdict. Lenses may strengthen the selected task, but must not change task responsibility.

Default to `Compatibility: preserve` and `Constraint Mode: respect`. Breaking compatibility or constraint exceptions require explicit user or explicit-source intent.

Task shortcut:

- `shape = synthesis`: ambiguous, what-if, strategy, conceptual, direction-setting, or entrypoint-selection requests.
- `explore = evidence`: code/docs/reference/behavior/entrypoint/dependency fact gathering.
- `review = verdict`: existing target reasonableness, readiness, conflict, safety, or acceptance checks.
- `plan = executable sequence`: chosen direction to implementable steps.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `explore` for what exists, where it is, how it appears to work, and how reliable the evidence is.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.
- Do not add the `consistency` lens for discovery questions.

Compatibility / constraint rule:

- `shape` may suggest breaking or override pressure, but cannot activate it automatically.
- `plan` must encode selected breaking or exception scope explicitly.
- `build` and bounded implement must stop on unplanned compatibility removal, alias removal, migration removal, fallback removal, or constraint bypass.
- `prototype_exception` is temporary PoC scope, not a durable project constraint.

Conceptual planning rule:

- `shape --lens conceptual` defaults to `Planning Level: concept`.
- `plan --lens conceptual` can produce `Planning Level: high-level-plan` or `Planning Level: implementation-plan`.
- `strategy` compares options; `conceptual` controls planning level.
- `/wf-build` does not require a `Planning Level` label; it still requires an explicit executable plan.

Exploration notes:

- Explicit `notes/**` targets may be persisted as disposable exploration notes through `Task: persist`.
- Do not treat `notes/**` as project docs or implementation plans.
- Do not use OpenCode bounded implement from `notes/**`; promote useful conclusions into `.session/**` or create an explicit external plan first.

## Usage Levels

### Level 1: Context Helper

Use this when OpenCode should summarize current state only.

- Read `.session/**`, `docs/**`, and relevant source files.
- Do not create a plan unless asked.
- Do not edit files.
- Return current state, relevant files, gaps, unknowns, and suggested next workflow task.

### Level 2: Draft Plan Generator

Use this when OpenCode native Plan can draft an implementation plan.

- Treat the result as a draft external plan.
- Do not edit files during native Plan.
- Audit the draft with `review` before implementation.
- Use `persist` to persist handoff plans to `.session/threads/{thread}/plan_{topic}.md`.

### Level 3: Bounded Implementer

Use this only after an explicit narrow plan exists.

- Implement only the explicit segment.
- Do not broaden scope.
- Do not touch `.session/**`, `.workflow/**`, `docs/**`, or unrelated files unless the explicit plan names them.
- Return compact `Execution Summary` by default; use full summary only for blocked, partial, failed verification, pitfalls, scope-expansion risk, or user-requested persistence.
- `Execution Summary` is factual, not a review verdict. Persist useful summaries through `persist` as `Artifact: note` and `Intent: audit`.
- Review the diff after implementation.

## Recommended Path

```text
OpenCode context summary
-> workflow review / shape / plan
-> Persist Packet -> persist thread handoff when useful
-> OpenCode bounded implement
-> workflow diff review
-> sync project docs when stable
```

OpenCode native Plan belongs to the `external-agent` path. It is not `Mode: execute`. `Mode: execute` remains workflow-managed execution through `Task: build`.

## Slash Commands

OpenCode project commands live in `.opencode/commands/**`. They are thin prompt shortcuts, not a separate workflow. Keep task, lens, role, and template semantics in `.workflow/**`.

Default commands:

- `/wf`: route a request and recommend the smallest path.
- `/wf-clarify`: clarify goal, scope, constraints, and success criteria.
- `/wf-explore`: extract read-only evidence from code, docs, references, or unfamiliar repos.
- `/wf-shape`: discuss direction, what-if, strategy, or conceptual design.
- `/wf-plan`: draft a repo-aware plan or external-agent handoff.
- `/wf-review`: review a plan, diff, code/docs claim, or readiness question.
- `/wf-persist`: persist `.session/**` or explicit `notes/**`.
- `/wf-build`: execute an explicit plan only.
- `/wf-sync`: sync `docs/**` or explicit `src/**/README.md`.

Command rules:

- Commands default to `Output: compact` unless persistence, sync, audit, diff review, or handoff detail is required.
- Commands must not load all tasks or all lenses.
- `/wf`, `/wf-shape`, and `/wf-plan` may recommend `/wf-build`, but they must not execute implementation.
- `/wf-build` is a high-risk write command. It requires an explicit `Plan:` and may not use `notes/**` as an execution source.
- Calling `/wf-build` authorizes only the listed plan scope; it does not authorize scope expansion.
- `/wf-explore` uses the read-only `workflow-context` agent.
- `/wf-review` uses the read-only `workflow-review` agent.
- `/wf-sync` uses the permission-gated `workflow-docs-sync` agent.
- Do not add `AGENTS.md` for this adapter; use commands and `.workflow/opencode.md` explicitly.

Examples:

```text
/wf-clarify 我想做一个 graph10x PoC，帮我收敛目标和成功标准
/wf-explore 帮我看这个第三方 repo 是否有调用链入口
/wf-shape 如果我要结合 graph10x 调用链分析来构建项目，起始入口怎么选？
/wf-review Audit this plan before implementation: <plan>
/wf-persist Artifact: shape Thread: graph10x Topic: entrypoints Source: last discussion
/wf-build Plan: .session/threads/auth/plan_auth.md
/wf-sync Source: .session/threads/auth/decision_auth_boundary.md Target: docs/architecture/auth.md Future Use: guide future auth changes
```

## Add Context

Use only the files needed for the current step.

- `.workflow/tasks/route.md` for choosing a path.
- `.workflow/tasks/clarify.md` for goal, scope, constraints, and success criteria.
- `.workflow/tasks/explore.md` for read-only evidence extraction.
- `.workflow/tasks/shape.md` for ambiguous, what-if, strategy, conceptual, or direction-setting work.
- `.workflow/tasks/review.md` for plan audit or diff review.
- `.workflow/tasks/plan.md` for a repo-aware handoff.
- `.workflow/tasks/persist.md` for session artifact persistence.
- `.workflow/tasks/build.md` for explicit-plan execution rules.
- `.workflow/tasks/sync.md` for project docs or code-adjacent README alignment.
- `.workflow/lenses/<lens>.md` only when explicitly selected.
- `.session/goal/*`, relevant `.session/inbox/**`, relevant `.session/threads/**`, `docs/**`, and source files as needed.
- explicit `notes/**` only when persisting disposable exploration notes.

Do not load all tasks or all lenses by default.

## Copyable Prompts

### Context Helper

```text
Use OpenCode as context helper only. Do not edit files and do not create an implementation plan.

Context:
- .session/goal/*
- .session/inbox/<relevant>
- .session/threads/<relevant>
- docs/<relevant>
- <source files>

Request:
Summarize the current implementation state against the target context.

Return:
- Current state
- Relevant files
- Gaps
- Unknowns
- Suggested next workflow task
```

### Draft Plan

```text
Use native Plan phase only. Create a draft implementation plan and do not edit files.

Goal:
<goal>

Context:
- .session/goal/*
- .session/threads/<relevant>
- docs/<relevant>
- <source files>

Plan requirements:
- Success criteria
- Compatibility: preserve | breaking
- Constraint Mode: respect | propose_override | prototype_exception
- Target files
- Allowed changes
- Do-not-touch areas
- Removed compatibility, migration/alias, and constraint exceptions when selected
- Assumptions
- Step-by-step verification
- Stop conditions
- Rollback or recovery
- Project docs impact
- Open questions
```

### Persist Thread Handoff

```text
Mode: persist
Task: persist
Artifact: plan
Status: working | stable | superseded
Thread: <thread-name>
Intent: handoff
Depth: detailed
Topic: <topic>
Request:
Persist the OpenCode handoff plan. Preserve decision-relevant reasoning, not full transcript.
```

`persist` can apply explicit edits, but it must not invent a new direction, re-plan work, or turn unclear `needs changes` content into a stable artifact. Route those cases back to `shape`, `plan`, or `review`.

Use `persist shape_<topic>` to reference a shape by `Artifact ID`. In multi-topic discussion, persist the original/main goal by default and keep later topics as supporting context unless explicitly targeted.

### Persist Exploration Note

```text
Mode: persist
Task: persist
Artifact: note
Status: inbox
Intent: exploration
Depth: compact | standard
Target: notes/<topic>.md
Request:
Persist this as a disposable exploration note. Do not treat it as project docs or an execution source.
```

### Plan Audit

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this OpenCode plan draft before implementation.

Return:
Decision: ready | needs changes | blocked | docs blocked

Check:
- Scope and target files
- Success criteria
- Step-by-step verification
- Source of truth
- Project Docs Rules if docs/** is touched
- Minimal diff risk
- Open questions
- Whether the plan exceeds the user's stated intent
```

### Bounded Implement

```text
Implement only the explicit external plan segment below. Do not broaden scope.
Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.

Explicit segment:
<explicit segment>

Do not modify .session/**, .workflow/**, docs/**, or unrelated files unless explicitly listed in the explicit segment.
Do not use notes/** as the explicit segment; notes are disposable exploration memory.
Stop and return to plan/review if implementation requires expanding scope, removing unplanned compatibility, or overriding unplanned constraints.

Report:
- Changed files
- Verification run for each major step
- Anything skipped and why
```

`/wf-build` is the command form of bounded implementation. Use it only when the explicit plan is present in `Plan:` or in the current prompt. If the plan is missing or not executable enough, `/wf-build` must return `missing_prerequisite` and avoid file edits.

`/wf-build` does not write `.session/**`. When execution pitfalls or blocked work should be remembered, persist the build output with `Task: persist`, `Artifact: note`, `Intent: audit`, and `Topic: <topic>_execution_summary`.

### Diff Review

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the OpenCode diff against the explicit external plan.

Check:
- Scope drift
- Missing edits
- Unrelated edits
- Missing verification
- Drive-by refactors
- Project Docs Rules issues
- Required follow-up
```

### Project Docs Sync

```text
Use OpenCode docs sync only for code-aligned project docs or code-adjacent README alignment.

Source:
- <session decision, diff, code, or existing docs>

Target:
- docs/<area>/<topic>.md or src/<area>/README.md

Future Use:
<how this doc guides future human/agent work>

Rules:
- Follow Project Docs Rules.
- Source, future use, source of truth, and future-use success criteria must be clear.
- Preserve existing docs structure and tone.
- Do not write .session/**, .workflow/**, source code, or unrelated docs.
- Output docs blocked if source, future use, source of truth, future-use success criteria, or safety is unclear.
```
