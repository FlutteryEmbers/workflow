# Codex Adapter

Codex support is a manual adapter. It does not add `AGENTS.md`, does not replace `.workflow/tasks/**`, and does not create a separate Codex workflow.

Use this file when you want Codex to follow Workflow Lite explicitly. Add only the task, selected lenses, templates, session artifacts, project docs, and source files needed for the current request.

## Core Rules

- `.workflow/README.md` is the workflow source of truth.
- Default to `Mode: discuss`.
- Default to `Output: compact` for general discussion; use `Output: normal` to refine before persist and `Output: full` for artifacts, handoffs, audits, or diff reviews.
- When unsure, start with `shape`. Use `explore` for evidence and `review` for verdict.
- Do not load all tasks, lenses, templates, or `.workflow/**` by default.
- Use one task as the main workflow context.
- Load lenses only when the user explicitly selects them.
- Use core `Abstraction Level` rules when the user wants concept-first planning, phase planning, low-level implementation planning, or strong-model-to-weak-model handoff.
- Codex may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- Load templates only for `persist` or `sync` in `Mode: persist`.
- Treat `.session/**` as working memory, not project source of truth.
- Treat `.session/threads/**` as session working memory grouped by small closable work item.
- Use explicit `.session/threads/{thread}/plan_{topic}.md` files for workflow-managed build input.
- Treat explicit `notes/**` targets as disposable exploration notes, not project docs or execution sources.
- Treat `docs/**` as code-aligned project docs and apply Project Docs Rules before writing.
- Lenses may strengthen the selected task, but must not change task responsibility.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`; breaking compatibility or constraint exceptions require explicit user or explicit-source intent.

## Task Boundary Shortcut

- `shape = synthesis`: ambiguous, what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests.
- `explore = evidence`: code/docs/reference/behavior/entrypoint/dependency fact gathering.
- `review = verdict`: existing target reasonableness, readiness, conflict, safety, or acceptance checks.
- `plan = planning sequence`: chosen direction to phases, repo-aware steps, or executable handoff.

## Discussion Freedom

Workflow Lite is human-in-the-loop first. In `Mode: discuss`, Codex may provide useful provisional thinking without treating it as approval.

- `shape`: `Provisional Recommendation`, `Best Guess`, `Candidate Options`, `What Would Change My Mind`.
- `explore`: `Candidate Interpretations`, `Likely Entry Points`, `Borrowable Ideas`.
- `review`: `Minimal Revision Sketch`, `Repair Direction`.
- `plan`: non-build-ready `Planning Draft` when execution detail is not requested yet.
- Add `Confidence`, `Assumptions`, and `Human Decision Needed` when uncertainty or impact is material.

Discussion freedom does not permit file writes, stable-document sync, implementation, or source-of-truth decisions.

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

Use `Abstraction Level: concept | phase-plan | implementation-plan` to separate direction, staged planning, and execution handoff.

- `shape` usually produces `concept`.
- `shape` records `Impact Surface` and `Recommended Next Abstraction Level` when it may feed planning.
- `plan` automatically chooses `phase-plan` or `implementation-plan` unless the user explicitly names one.
- `plan` inherits a shape artifact's `Recommended Next Abstraction Level` when present; without that, infer from the request and read-only preflight, defaulting to `phase-plan` when uncertain.
- `implementation-plan` is only for build-ready planning, external-agent handoff, weak-model handoff, or explicit execution preparation.
- Option comparison is built into `shape`; abstraction level is core protocol.
- `build` does not require an `Abstraction Level` label; it requires an explicit plan concrete enough to execute safely.

## Common Paths

### External Goal Intake

Use one of two paths:

- Long or reusable external source: persist `Artifact: brief` with `Brief Type: external-goal`, then shape from that inbox brief and persist the shape.
- Current conversational goal: shape directly in chat, then persist the shape if it is worth preserving.

`shape` is the reasoned projection from chat or an inbox goal brief. The durable shaped result belongs in `.session/threads/{thread}/shape_{topic}.md`.

### Discuss

Use for design discussion, code understanding, route selection, critique, planning conversation, or preflight.

```text
Mode: discuss
Task: <route|clarify|explore|shape|plan|review|sync>
Lens: <none or explicit lenses>
Context:
- .workflow/tasks/<task>.md
- .workflow/lenses/<lens>.md only when selected
- relevant .session/inbox/** and .session/threads/**
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
Artifact: <brief|note|shape|option|plan|review|decision|distillation|expanded>
Brief Type: <general|external-goal; only for Artifact: brief>
Artifact State: <inbox|working|settled|superseded>
Thread: <thread-name>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference>
Depth: <compact|standard|detailed>
Topic: <topic>
Target: <optional when thread/topic can infer target>
Context:
- .workflow/tasks/persist.md
- .workflow/templates/<artifact template>.md
- selected lenses only when named
- Persist Packet or source context
Request:
Persist the high-fidelity structured artifact only.
```

`persist` may infer `.session/inbox/**` and `.session/threads/{thread}/{artifact}_{topic}.md`. Thread directories are small closable work items. When `Thread` is absent, infer the target by same-work-item fit: reuse active threads for continuations/refinements/review responses, create or suggest a new thread for distinct bounded decisions or changes, and treat recency as supporting evidence only. Explicit `notes/**` targets are allowed only for disposable exploration notes and are never inferred. `.session/archive/**` targets route to `sync` with `Sync Domain: session-archive`. Targets outside active `.session/inbox/**`, `.session/threads/**`, and `notes/**` route to `sync`, `build`, or external-agent.

Use `Persist Packet` when available. Preserve decision-relevant reasoning, not full transcript. Keep context, key facts, decision trail, rejected options, risks, examples, and next use when they affect later work.

Use `persist shape_<topic>` to reference a shape by `Artifact ID`. This anchors source context and does not derive the thread directory. Include `Thread Inference Note` when the inferred target depends on same-work-item assumptions, related old threads, or low-confidence fit.

`persist` may restructure artifacts and apply explicit review edits. It must not choose a new direction, re-plan execution, judge whether review feedback is correct, or turn unclear `needs changes` content into a settled artifact; route those cases back to `shape`, `plan`, or `review`.

`notes/**` is not an execution source. Settled conclusions should be promoted through normal workflow into `.session/threads/**` or `docs/**`.

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

After build, output compact `Execution Summary` by default. Use full summary only for blocked, partial, failed verification, pitfall, scope-expansion risk, or user-requested persistence. `Execution Summary` is factual, not a review verdict, and `build` must not write `.session/**`; persist useful summaries as `Artifact: note` with `Intent: audit`.

### External-Agent Native Plan / Implement

Use when Codex native Plan -> Implement should edit files directly.

```text
Codex native Plan
-> review audit
-> Codex Implement
-> review diff
```

The native plan is a draft until reviewed or explicitly chosen for implementation. Persist handoffs with `persist` to `.session/threads/{thread}/plan_{topic}.md`.

### Stable Document Sync

Use `sync` for stable-document projection.

```text
Mode: persist
Task: sync
Lens: <none|consistency|language|architecture|distill>
Sync Domain: <project-docs | session-archive>
Sync Object: <architecture | feature | reference | code-readme | archive-summary | all>
Scope: <area, code/docs scope, source thread, or archive scope>
Source Of Truth: <code | diff | session thread artifact | explicit user decision | existing docs>
Target Directory: <optional docs directory, src area, or archive directory>
Target: docs/<target>.md or src/{area}/README.md or .session/archive/{thread}/summary.md
Source:
- .session/threads/<thread>/<artifact>.md
Context:
- .workflow/tasks/sync.md
- .workflow/templates/sync.md, plus .workflow/templates/project_doc.md, .workflow/templates/code_readme.md, or .workflow/templates/archive_summary.md when creating a target
- source artifact, existing docs, and relevant source files
Request:
Project confirmed outcomes into the target only. Use Sync Object gates for `project-docs`; use archive prerequisites for `session-archive`.
```

When creating a new `architecture`, `feature`, or `reference` docs target, add `.workflow/templates/project_doc.md`. When updating existing docs, preserve the target file's structure.

If the source is only `.session/inbox/**` or `notes/**`, require explicit source-of-truth confirmation before writing project docs.

For `session-archive`, require `Source Thread`, `Thread Status`, `Archive Purpose`, `Summary Scope`, `Next Retrieval Use`, and target `.session/archive/<thread>/summary.md`. Do not edit active `.session/threads/**`.

## Copyable Prompts

### Discuss Direction

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: shape
Lens: <none or selected lenses>
Request:
Discuss the target direction. Include evidence, unknowns, tradeoffs, and a short Persist Candidate when worth preserving. Do not write files.
```

### Persist Artifact

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: persist
Artifact: <artifact>
Artifact State: <inbox|working|settled|superseded>
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
Artifact State: inbox
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

### Stable Document Sync

```text
Use .workflow/codex.md as the Codex adapter.
Mode: persist
Task: sync
Sync Domain: project-docs
Sync Object: architecture
Scope: <area>
Source Of Truth: <code | diff | session thread artifact | explicit user decision | existing docs>
Target: docs/architecture.md
Source:
- .session/threads/<thread>/<artifact>.md
Alignment Success Criteria:
<what code/docs alignment mistake this sync should prevent>
Request:
Sync confirmed facts into code-aligned project docs. Follow Project Docs Rules and preserve existing docs tone and structure.
```

For archive summaries, set `Sync Domain: session-archive`, `Source Thread: .session/threads/<thread>/**`, `Thread Status`, `Archive Purpose`, `Summary Scope`, `Next Retrieval Use`, and `Target: .session/archive/<thread>/summary.md`.

## When Not To Use Codex Adapter

- If the user wants Copilot Add Context guidance, use `.workflow/copilot.md`.
- If the user wants OpenCode-specific behavior, use `.workflow/opencode.md`.
- If the request is ordinary repo work without workflow boundaries, Codex can operate normally, but must still respect explicit user instructions.
