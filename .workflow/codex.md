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
- Codex may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- Load templates only for `save` or `sync` in `Mode: persist`.
- Treat `.session/**` as working memory, not project source of truth.
- Treat `.session/drafts/**` as unapproved working artifacts.
- Prefer `.session/accepted/**` for approved plans, accepted decisions, and implementation handoffs.
- Treat explicit `notes/**` targets as disposable exploration notes, not project docs or approved sources.
- Treat `docs/**` as code-aligned project docs and apply Project Docs Rules before writing.
- Lenses may strengthen the selected task, but must not change task responsibility.

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
- .session/goal/* and relevant .session/inbox/**, .session/drafts/**, .session/accepted/**
- docs/** or source files when relevant
Request:
<what you want>
```

Do not add templates. Do not write files.

### Save Session Artifact

Use when the user wants to save a session artifact from recent discussion, an existing draft, or user-provided source.

```text
Mode: persist
Task: save
Artifact: <brief|note|shape|option|plan|review|decision|distillation|expanded|goal>
Status: <inbox|draft|accepted>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference>
Depth: <compact|standard|detailed>
Topic: <topic>
Target: <optional for inbox/drafts; explicit for accepted/goal when needed>
Context:
- .workflow/tasks/save.md
- .workflow/templates/<artifact template>.md
- selected lenses only when named
- Save Packet or source context
Request:
Save the high-fidelity structured artifact only.
```

`save` may infer `.session/inbox/**` and `.session/drafts/**`. Accepted artifacts require explicit accepted, approved, or promote intent. Explicit `notes/**` targets are allowed only for disposable exploration notes and are never inferred. Targets outside `.session/**` and `notes/**` route to `sync`, `build`, or external-agent.

Use `Save Packet` when available. Preserve decision-relevant reasoning, not full transcript. Keep context, key facts, reasoning trail, rejected options, risks, examples, and next use when they affect later work.

`notes/**` is not an approved execution source. Stable conclusions should be promoted through normal workflow into `.session/**` or `docs/**`.

### Workflow-Managed Execute

Use when the user wants Workflow Lite to execute an approved plan.

```text
Mode: execute
Write Path: workflow-managed
Task: build
Lens: <none|test|debug>
Plan: .session/accepted/plan_{topic}.md
Context:
- .workflow/tasks/build.md
- approved plan
- target files named by the plan
Request:
Implement only the approved plan.
```

If the approved plan is missing, unclear, or points only to `.session/drafts/**` without explicit approval, do not edit files.

### External-Agent Native Plan / Implement

Use when Codex native Plan -> Implement should edit files directly.

```text
Codex native Plan
-> review audit
-> Codex Implement
-> review diff
```

The native plan is a draft until reviewed. Persist draft handoffs with `save` to `.session/drafts/**`; persist accepted handoffs with `save` to `.session/accepted/**`.

### Project Docs Sync

Use `sync` for code-aligned project docs and code-adjacent README alignment.

```text
Mode: persist
Task: sync
Lens: <none|consistency|language|domain|architecture|distill>
Target: docs/{area}/{topic}.md or src/{area}/README.md
Source:
- .session/accepted/<artifact>.md
Context:
- .workflow/tasks/sync.md
- .workflow/templates/sync.md or .workflow/templates/code_readme.md
- source artifact, existing docs, and relevant source files
Request:
Sync confirmed facts into the target only.
```

When creating a new `docs/**` target, add `.workflow/templates/project_doc.md` or `.workflow/templates/architecture_note.md`. When updating existing docs, preserve the target file's structure.

If the source is only `.session/drafts/**`, require explicit source-of-truth confirmation before writing project docs.

## Copyable Prompts

### Discuss Direction

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: shape
Lens: <none or selected lenses>
Request:
Discuss the target direction. Include evidence, unknowns, tradeoffs, and a Save Packet when worth preserving. Do not write files.
```

### Save Artifact

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: save
Artifact: <artifact>
Status: <inbox|draft|accepted>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference>
Depth: <compact|standard|detailed>
Topic: <topic>
Request:
Save the current converged session state. Preserve decision-relevant reasoning, not full transcript.
```

### Save Exploration Note

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: save
Artifact: note
Status: inbox
Intent: exploration
Depth: compact | standard
Target: notes/<topic>.md
Request:
Save this as a disposable exploration note. Do not treat it as project docs or an approved source.
```

### Native Plan

```text
Use Codex native Plan phase only. Do not edit files.

Goal:
<goal>

Plan requirements:
- Success criteria
- Target files
- Allowed changes
- Do-not-touch areas
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
Audit this Codex native plan before implementation. Return approved, needs changes, blocked, or docs blocked.
```

### Bounded Implement

```text
Implement only the approved external plan segment below. Do not broaden scope.
Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.
Stop and return to plan/review if implementation requires expanding scope.
```

### Diff Review

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the Codex diff against the approved external plan.
```

### Project Docs Sync

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: sync
Target: docs/<area>/<topic>.md
Source:
- .session/accepted/<artifact>.md
Future Use:
<how this doc guides future human/agent work>
Request:
Sync confirmed facts into code-aligned project docs. Follow Project Docs Rules and preserve existing docs tone and structure.
```

## When Not To Use Codex Adapter

- If the user wants Copilot Add Context guidance, use `.workflow/copilot.md`.
- If the user wants OpenCode-specific behavior, use `.workflow/opencode.md`.
- If the request is ordinary repo work without workflow boundaries, Codex can operate normally, but must still respect explicit user instructions.
