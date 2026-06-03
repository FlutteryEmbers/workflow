# Codex Adapter

Codex support is a manual adapter. It does not add `AGENTS.md`, does not replace `.workflow/tasks/**`, and does not create a separate Codex workflow.

Use this file when you want Codex to follow Workflow Lite explicitly. Add only the task, selected lenses, templates, session artifacts, project docs, and source files needed for the current request.

## Core Rules

- `.workflow/README.md` is the workflow source of truth.
- Default to `Mode: discuss`.
- When unsure, start with `shape`. Use `explore` for evidence and `review` for verdict.
- Do not load all tasks, lenses, templates, or `.workflow/**` by default.
- Use one task as the main workflow context.
- Load lenses only when the user explicitly selects them.
- Use `conceptual` only when the user explicitly wants concept-first planning, high-level planning, low-level implementation planning, or strong-model-to-weak-model handoff.
- Codex may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- Load templates only for `persist` or `sync` in `Mode: persist`.
- Treat `.session/**` as working memory, not project source of truth.
- Treat `.session/threads/**` as session working memory grouped by discussion/work thread.
- Use explicit `.session/threads/{thread}/plan_{topic}.md` files for workflow-managed build input.
- Treat explicit `notes/**` targets as disposable exploration notes, not project docs or execution sources.
- Treat `docs/**` as code-aligned project docs and apply Project Docs Rules before writing.
- Lenses may strengthen the selected task, but must not change task responsibility.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`; breaking compatibility or constraint exceptions require explicit user or explicit-source intent.

## Task Boundary Shortcut

- `shape = synthesis`: ambiguous, what-if, strategy, conceptual, direction-setting, or entrypoint-selection requests.
- `explore = evidence`: code/docs/reference/behavior/entrypoint/dependency fact gathering.
- `review = verdict`: existing target reasonableness, readiness, conflict, safety, or acceptance checks.
- `plan = executable sequence`: chosen direction to implementable steps.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `explore` for what exists, where it is, how it appears to work, and how reliable the evidence is.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.
- Do not add the `consistency` lens for discovery questions.

## Compatibility / Constraint Policy

- `shape` may identify compatibility pressure and constraint tension, but must not switch to `Compatibility: breaking` or `Constraint Mode != respect` automatically.
- `plan` must name removed compatibility, migration/alias decisions, constraint exceptions, do-not-preserve items, cleanup, and stop conditions when breaking or exception mode is selected.
- `build` or native implement must stop if it needs unplanned compatibility removal or constraint override.
- `prototype_exception` is temporary PoC scope, not a durable project constraint.

## Conceptual Planning

When `Lens: conceptual` is explicitly selected, state `Planning Level: concept | high-level-plan | implementation-plan`.

- `shape` usually produces `concept`.
- `plan` may produce `high-level-plan` or `implementation-plan`.
- `strategy` compares options; `conceptual` controls the level.
- `build` does not require a `Planning Level` label; it requires an explicit plan concrete enough to execute safely.

## Common Paths

### Discuss

Use for design discussion, code understanding, route selection, critique, planning conversation, or preflight.

```text
Mode: discuss
Task: <route|clarify|explore|shape|plan|review|sync>
Lens: <none or explicit lenses>
Context:
- .workflow/tasks/<task>.md
- .workflow/lenses/<lens>.md only when selected
- .session/goal/* and relevant .session/inbox/**, .session/threads/**
- docs/** or source files when relevant
Request:
<what you want>
```

Do not add templates. Do not write files.

### Persist Session Artifact

Use when the user wants to persist a session artifact from recent discussion, an existing draft, or user-provided source.

```text
Mode: persist
Task: persist
Artifact: <brief|note|shape|option|plan|review|decision|distillation|expanded|goal>
Status: <inbox|working|stable|superseded>
Thread: <thread-name>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference>
Depth: <compact|standard|detailed>
Topic: <topic>
Target: <optional when thread/topic can infer target; explicit for goal when needed>
Context:
- .workflow/tasks/persist.md
- .workflow/templates/<artifact template>.md
- selected lenses only when named
- Persist Packet or source context
Request:
Persist the high-fidelity structured artifact only.
```

`persist` may infer `.session/inbox/**` and `.session/threads/{thread}/{artifact}_{topic}.md`. Explicit `notes/**` targets are allowed only for disposable exploration notes and are never inferred. Targets outside `.session/**` and `notes/**` route to `sync`, `build`, or external-agent.

Use `Persist Packet` when available. Preserve decision-relevant reasoning, not full transcript. Keep context, key facts, reasoning trail, rejected options, risks, examples, and next use when they affect later work.

Use `persist shape_<topic>` to reference a shape by `Artifact ID`. In multi-topic discussion, persist the original/main goal by default and keep later topics as supporting context unless explicitly targeted.

`persist` may restructure artifacts and apply explicit review edits. It must not choose a new direction, re-plan execution, judge whether review feedback is correct, or turn unclear `needs changes` content into a stable artifact; route those cases back to `shape`, `plan`, or `review`.

`notes/**` is not an execution source. Stable conclusions should be promoted through normal workflow into `.session/**` or `docs/**`.

### Workflow-Managed Execute

Use when the user wants Workflow Lite to execute an explicit plan.

```text
Mode: execute
Write Path: workflow-managed
Task: build
Lens: <none|test|debug>
Plan: .session/threads/{thread}/plan_{topic}.md
Context:
- .workflow/tasks/build.md
- explicit plan
- target files named by the plan
Request:
Implement only the explicit plan.
```

If the plan is missing, unclear, or not executable enough, do not edit files.

### External-Agent Native Plan / Implement

Use when Codex native Plan -> Implement should edit files directly.

```text
Codex native Plan
-> review audit
-> Codex Implement
-> review diff
```

The native plan is a draft until reviewed or explicitly chosen for implementation. Persist handoffs with `persist` to `.session/threads/{thread}/plan_{topic}.md`.

### Project Docs Sync

Use `sync` for code-aligned project docs and code-adjacent README alignment.

```text
Mode: persist
Task: sync
Lens: <none|consistency|language|domain|architecture|distill>
Target: docs/{area}/{topic}.md or src/{area}/README.md
Source:
- .session/threads/<thread>/<artifact>.md
Context:
- .workflow/tasks/sync.md
- .workflow/templates/sync.md or .workflow/templates/code_readme.md
- source artifact, existing docs, and relevant source files
Request:
Sync confirmed facts into the target only.
```

When creating a new `docs/**` target, add `.workflow/templates/project_doc.md` or `.workflow/templates/architecture_note.md`. When updating existing docs, preserve the target file's structure.

If the source is only `.session/inbox/**` or `notes/**`, require explicit source-of-truth confirmation before writing project docs.

## Copyable Prompts

### Discuss Direction

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: shape
Lens: <none or selected lenses>
Request:
Discuss the target direction. Include evidence, unknowns, tradeoffs, and a short Persist Hint when worth preserving. Do not write files.
```

### Persist Artifact

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: persist
Artifact: <artifact>
Status: <inbox|working|stable|superseded>
Thread: <thread-name>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference>
Depth: <compact|standard|detailed>
Topic: <topic>
Request:
Persist the current converged session state. Preserve decision-relevant reasoning, not full transcript.
```

For a shape, you can also request `persist shape_<topic>` to avoid latest-topic drift.

### Persist Exploration Note

```text
Use .workflow/codex.md as the Codex adapter.
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

### Native Plan

```text
Use Codex native Plan phase only. Do not edit files.

Goal:
<goal>

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
- Project docs follow-up
- Open questions
```

### Plan Audit

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this Codex native plan before implementation. Return ready, needs changes, blocked, or docs blocked.
```

### Bounded Implement

```text
Implement only the explicit external plan segment below. Do not broaden scope.
Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.
Stop and return to plan/review if implementation requires expanding scope, removing unplanned compatibility, or overriding unplanned constraints.
```

### Diff Review

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the Codex diff against the explicit external plan.
```

### Project Docs Sync

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: sync
Target: docs/<area>/<topic>.md
Source:
- .session/threads/<thread>/<artifact>.md
Future Use:
<how this doc guides future human/agent work>
Request:
Sync confirmed facts into code-aligned project docs. Follow Project Docs Rules and preserve existing docs tone and structure.
```

## When Not To Use Codex Adapter

- If the user wants Copilot Add Context guidance, use `.workflow/copilot.md`.
- If the user wants OpenCode-specific behavior, use `.workflow/opencode.md`.
- If the request is ordinary repo work without workflow boundaries, Codex can operate normally, but must still respect explicit user instructions.
