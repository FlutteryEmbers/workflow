# OpenCode Adapter

OpenCode support is a thin adapter. It does not replace `.workflow/tasks/**`, `.workflow/lenses/**`, or `.workflow/templates/**`, and it does not create a separate OpenCode workflow.

Use OpenCode when it helps with context reading, plan drafting, or bounded implementation. Keep `.workflow/**` as the source of truth for task semantics, lenses, write boundaries, and Project Docs Rules.

OpenCode may suggest `redteam` when risk triggers match, but must not auto-load or apply it. Use full `redteam` only when the user explicitly selected that lens or the prompt explicitly includes it.

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

## Add Context

Use only the files needed for the current step.

- `.workflow/tasks/route.md` for choosing a path.
- `.workflow/tasks/review.md` for plan audit or diff review.
- `.workflow/tasks/plan.md` for a repo-aware handoff.
- `.workflow/tasks/persist.md` for session artifact persistence.
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
