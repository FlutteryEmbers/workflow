# Codex Adapter

Codex support is a manual adapter. It does not add `AGENTS.md`, does not replace `.workflow/tasks/**`, and does not create a separate Codex workflow.

Use this file when you want Codex to follow Workflow Lite explicitly. Add only the task, selected lenses, templates, session artifacts, formal docs, and source files needed for the current request.

## Core Rules

- `.workflow/README.md` is the workflow source of truth.
- Default to `Mode: discuss`.
- Do not load all tasks, lenses, templates, or `.workflow/**` by default.
- Use one task as the main workflow context.
- Load lenses only when the user explicitly selects them.
- Codex may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- Load templates only in `Mode: persist`.
- Treat `.session/**` as working memory, not formal source of truth.
- Treat `.session/drafts/**` as unapproved working artifacts.
- Prefer `.session/accepted/**` for approved plans, accepted decisions, and implementation handoffs.
- Treat `docs/**` as formal project documentation and apply Formal Docs Rules before writing.

## Write Paths

- `workflow-managed`: use Workflow Lite modes and tasks.
- `external-agent`: use Codex native Plan -> Implement, then audit the plan before implementation and review the diff afterward.

`Mode: execute` is workflow-managed execution through `Task: build`. Codex native Plan -> Implement is `Write Path: external-agent`; it is not `Mode: execute`.

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

### Persist Session Artifact

Use when the user wants to save a session artifact.

```text
Mode: persist
Task: <clarify|explore|shape|plan|review>
Lens: <none or explicit lenses>
Target: .session/<inbox|drafts|accepted>/<artifact_kind>_{topic}.md
Context:
- .workflow/tasks/<task>.md
- .workflow/templates/<template>.md
- selected lenses only when named
- source context
Request:
Write the target artifact only.
```

Ordinary `persist` writes only `.session/**`.

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

The native plan is a draft until reviewed. Persist draft handoffs to `.session/drafts/**`; persist accepted handoffs to `.session/accepted/**`.

### Formal Docs Sync

Use `sync` for formal docs and code-adjacent README alignment.

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

If the source is only `.session/drafts/**`, require explicit source-of-truth confirmation before writing formal docs.

## Copyable Prompts

### Context Summary

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: explore
Lens: none
Request:
Summarize the current state from the provided context. Do not write files.

Return:
- Current state
- Relevant files
- Gaps
- Unknowns
- Suggested next workflow task
```

### Discuss Direction

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: shape
Lens: <none or selected lenses>
Request:
Discuss the target direction. Include evidence, unknowns, tradeoffs, and a suggested persist target. Do not write files.
```

### Persist Artifact

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: <clarify|explore|shape|plan|review>
Lens: <none or selected lenses>
Target: .session/<inbox|drafts|accepted>/<artifact_kind>_{topic}.md
Request:
Write only the target artifact using the matching template.
```

### Native Plan

```text
Use Codex native Plan phase only. Do not edit files.

Goal:
<goal>

Context:
- .session/goal/*
- relevant .session/inbox/**, .session/drafts/**, .session/accepted/**
- docs/<relevant>
- <source files>

Plan requirements:
- Success criteria
- Target files
- Allowed changes
- Do-not-touch areas
- Assumptions
- Step-by-step verification
- Stop conditions
- Rollback or recovery
- Formal docs impact
- Open questions
```

### Plan Audit

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this Codex native plan before implementation.

Return:
Decision: approved | needs changes | blocked | docs blocked

Check:
- Scope and target files
- Success criteria
- Step-by-step verification
- Source of truth
- Formal Docs Rules if docs/** is touched
- Minimal diff risk
- Open questions
- Whether the plan exceeds the user's stated intent
```

### Bounded Implement

```text
Implement only the approved external plan segment below. Do not broaden scope.
Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.

Approved segment:
<approved segment>

Do not modify .session/**, .workflow/**, docs/**, or unrelated files unless explicitly listed in the approved segment.
Stop and return to plan/review if implementation requires expanding scope.

Report:
- Changed files
- Verification run for each major step
- Anything skipped and why
```

### Diff Review

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the Codex diff against the approved external plan.

Check:
- Scope drift
- Missing edits
- Unrelated edits
- Missing verification
- Drive-by refactors
- Formal Docs Rules issues
- Required follow-up
```

### Formal Docs Sync

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: sync
Lens: <none or selected lenses>
Target: docs/<area>/<topic>.md
Source:
- .session/accepted/<artifact>.md
Audience:
<reader>
Request:
Sync confirmed facts into formal docs. Follow Formal Docs Rules and preserve existing docs tone and structure.
```

## When Not To Use Codex Adapter

- If the user wants Copilot Add Context guidance, use `.workflow/copilot.md`.
- If the user wants OpenCode-specific behavior, use `.workflow/opencode.md`.
- If the request is ordinary repo work without workflow boundaries, Codex can operate normally, but must still respect explicit user instructions.
