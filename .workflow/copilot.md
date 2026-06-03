# Copilot Context Guide

Use this file as a manual Add Context menu. Prefer one mode, one task, selected lenses, and only the files needed for the current request.

## Mode Format

```text
Mode: <discuss|persist|execute>
Output: <compact|normal|full>
Task: <route|clarify|explore|shape|plan|persist|build|review|sync>
Lens: <none|iteration|expand|consistency|distill|language|domain|strategy|conceptual|redteam|test|architecture|debug>
Artifact: <required for persist unless target is explicit>
Status: <inbox|working|stable|superseded; for persist metadata>
Thread: <thread-name; for persist thread target inference>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference; for persist>
Depth: <compact|standard|detailed; optional for persist>
Topic: <file-safe topic; for inferred persist target>
Target: <optional for persist; required for sync and explicit writes>
Plan: <required for execute; otherwise none>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/lenses/<lens>.md only when selected
- #.workflow/templates/<template>.md only for persist or sync in Mode: persist
- #.session/goal/*, relevant .session/inbox/**, relevant .session/threads/**, docs/**, or source files
Request:
<what you want>
```

## Mode Rules

- `Mode: discuss` is the default. Add the task, selected lenses, and relevant context. Do not add templates. Do not create or update files.
- `Task: persist` in `Mode: persist` writes session artifacts to `.session/**`.
- `Task: sync` in `Mode: persist` writes only `docs/**` or explicit `src/**/README.md`.
- `Mode: execute` applies an explicit workflow-managed plan through `Task: build`.
- Native Codex/Copilot Plan -> Implement is the `external-agent` write path. It is not a Workflow Mode and does not use `Task: build`.

## Output Budget

Default to `Output: compact`.

Protocol: `Output: compact | normal | full`.

- `compact`: use `Understanding`, 3-6 answer bullets, 0-3 risk/unknown bullets, and optional one-line `Persist Hint`.
- `normal`: add short rationale or evidence when it materially improves the answer.
- `full`: use only for explicit persist requests, artifact previews, plan handoff, external-agent audit, diff review, or complex composite routing.

In `Mode: discuss`, do not output a full `Persist Packet` by default. Use `Persist Hint` instead:

```text
Persist Hint: Artifact=<artifact>; Thread=<thread>; Topic=<topic>; Suggested Target=<path>
```

Full `Persist Packet` is allowed only when the user asks to persist, says `Output: full`, asks for a handoff/audit, or the current response is the source for a following `Task: persist`.

## Task Boundary Shortcut

When unsure, start with `shape`. Use `explore` for evidence and `review` for verdict.

- `shape = synthesis`: ambiguous, what-if, strategy, conceptual, direction-setting, or entrypoint-selection requests.
- `explore = evidence`: code/docs/reference/behavior/entrypoint/dependency fact gathering.
- `review = verdict`: existing target reasonableness, readiness, conflict, safety, or acceptance checks.
- `plan = executable sequence`: chosen direction to implementable steps.

Lenses may strengthen the selected task, but must not change task responsibility.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `explore` for what exists, where it is, how it appears to work, and how reliable the evidence is.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.
- Do not add the `consistency` lens for discovery questions.

## Lens Selection Rules

- Default to `Lens: none`.
- Multiple lenses are allowed in `Mode: discuss` only when the user explicitly lists them.
- Copilot may recommend lenses, but must not load or apply them automatically.
- Copilot may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- In multi-lens discuss, organize output in the user's lens order, then provide a converged recommendation and `Persist Hint` when worth preserving.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens. If more lenses are needed, split into multiple persist steps.

`conceptual` is useful for strong-model-to-weak-model handoff. When explicitly selected, ask for or infer the current `Planning Level: concept | high-level-plan | implementation-plan`. `strategy` compares routes; `conceptual` controls the abstraction level.

## Write Boundaries

- `discuss`: no writes.
- `persist` persist: write `.session/**` or explicit `notes/**` disposable exploration notes.
- `sync` persist: write only `docs/**` or `src/**/README.md`.
- `execute`: may modify broader repository artifacts only when the explicit plan says so.
- `external-agent`: native Plan/Implement may write files directly, but the native plan must be audited before implementation and the diff must be reviewed afterward.
- `build` outputs an `Execution Summary`; it does not write `.session/**`. Persist useful summaries with `Task: persist`, `Artifact: note`, and `Intent: audit`.

## Compatibility / Constraint Policy

- Default to `Compatibility: preserve` and `Constraint Mode: respect`.
- `shape` may suggest `consider breaking` or `consider override`, but must not activate it unless the user explicitly requested it.
- `plan` must encode any explicit breaking or constraint exception into removed compatibility, migration/alias, do-not-preserve, cleanup, and stop conditions.
- `build` may execute breaking changes or constraint exceptions only when the plan explicitly allows them.
- Use `Constraint Mode: prototype_exception` only for temporary PoC scope; do not treat it as durable project docs content until confirmed.

## Persist Context

Use this when the user says persist, write, generate, update, land, or record a session artifact.

Add:

- #.workflow/tasks/persist.md
- the matching artifact template from #.workflow/templates/
- selected #.workflow/lenses/<lens>.md only when named
- `Persist Hint`, `Persist Packet`, source discussion, source artifact, or relevant context

Target rules:

- `.session/inbox/**` and `.session/threads/{thread}/{artifact}_{topic}.md` may be inferred from `Artifact + Thread + Topic`.
- `Target Directory` may be used to choose a specific thread folder.
- `.session/goal/**` requires an explicit target.
- Explicit `.session/**` targets should be respected even if naming differs from the recommended prefix.
- Explicit `notes/**` targets are allowed for disposable exploration notes. Never infer `notes/**`.
- `docs/**` or `src/**/README.md` targets route to `sync`.
- Code, `.workflow/**`, `.github/**`, or other repo artifacts route to `build` or external-agent.

Content fidelity:

- `persist` should consume `Persist Packet` when available, but it may also consume `Persist Hint` plus recent discussion.
- Use `persist shape_<topic>` to reference a shape by `Artifact ID`.
- In multi-topic discussion, persist the original/main goal by default; treat later topics as supporting context unless explicitly targeted.
- Preserve decision-relevant reasoning, not full transcript.
- Keep context, key facts, reasoning trail, rejected options, risks, examples, and next use when they affect later work.
- Use artifact `Depth: detailed` for shape, option, plan, review, decision, distillation, and expanded artifacts unless the user asks for compact artifact output.
- For `notes/**`, compact or standard depth is enough unless the user asks for more detail.

Revision boundary:

- `persist` may restructure an artifact and apply explicit review edits.
- `persist` must not choose a new direction, re-plan execution, judge whether review feedback is correct, or promote unclear `needs changes` content.
- If revision needs new judgment, route back to `shape`, `plan`, or `review`.

Exploration notes:

- `notes/**` is disposable exploration memory, not project docs.
- `notes/**` is not an execution source for build or external-agent implementation.
- Stable conclusions should later be persisted to `.session/**` or synced to `docs/**`.
- If `notes/**` grows beyond five active notes, suggest an optional `notes/INDEX.md`.

## Task Boundary Check

Before acting, classify the request when it is not obviously a fit:

- `fits`: the current task can handle it directly.
- `fits_with_preflight`: the current task can handle it after a read-only preflight.
- `composite`: the request needs multiple tasks.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: required target, explicit plan, source of truth, or project docs safety is missing.

If not `fits`, do not force-fit the request.

## Composite Task Segmentation

Common segmentations:

- Judge current code/docs and decide what to do: `review -> Persist Hint -> shape/plan -> persist`.
- Implement a feature from target docs and current code: `plan -> Persist Hint -> persist thread plan -> review -> external-agent/build -> review`.
- Move stable thread conclusions into project docs: `shape/plan/review -> Persist Hint -> persist thread artifact -> sync`.
- Distill a reference and improve workflow: `explore --lens distill -> Persist Hint -> shape -> persist thread artifact -> plan -> build`.
- Ambiguous what-if or entrypoint selection: `shape`, then `explore -> shape` only if missing evidence could change the recommendation.
- Understand code/docs mismatches as discovery: `explore -> Persist Hint -> persist note`, with `Reliability Notes`; use `review --lens consistency` only for source-of-truth judgments.

Use stop points before implementation and project docs sync.

## Template Map For Persist

- `Artifact: brief`: #.workflow/templates/brief.md
- `Artifact: note`: #.workflow/templates/note.md
- `Artifact: shape`: #.workflow/templates/shape.md
- `Artifact: option`: #.workflow/templates/options.md
- `Artifact: plan`: #.workflow/templates/plan.md
- `Artifact: review`: #.workflow/templates/review.md
- `Artifact: decision`: #.workflow/templates/decision.md
- `Artifact: distillation`: #.workflow/templates/distillation.md
- `Artifact: expanded`: #.workflow/templates/expanded.md
- `Artifact: goal`: #.workflow/templates/goal.md

## Common Scenarios

### Discuss Direction

```text
Mode: discuss
Task: shape
Lens: none or selected lenses
```

Add #.workflow/tasks/shape.md, selected lenses, and relevant context. Do not add templates.

### Persist Thread Shape

```text
Mode: persist
Task: persist
Artifact: shape
Status: working
Intent: exploration
Depth: detailed
Thread: graph10x
Topic: graph10x_entrypoints
```

Add #.workflow/tasks/persist.md and #.workflow/templates/shape.md. Target may be inferred as `.session/threads/graph10x/shape_graph10x_entrypoints.md`.
You may also say `persist shape_graph10x_entrypoints` to anchor the persist request to the shape artifact instead of the latest discussion topic.

### Persist Exploration Note

```text
Mode: persist
Task: persist
Artifact: note
Status: inbox
Intent: exploration
Depth: compact | standard
Target: notes/graph10x.md
```

Add #.workflow/tasks/persist.md and #.workflow/templates/note.md. `notes/**` must be explicit and remains disposable exploration memory.

### Persist Thread Decision

```text
Mode: persist
Task: persist
Artifact: decision
Status: stable
Intent: constraint
Depth: detailed
Thread: auth
Topic: auth_boundary
```

Add #.workflow/tasks/persist.md and #.workflow/templates/decision.md.

### Sync Project Docs

```text
Mode: persist
Task: sync
Target: docs/{area}/{topic}.md
Source:
- .session/threads/{thread}/decision_{topic}.md
```

Add #.workflow/tasks/sync.md, #.workflow/templates/sync.md, relevant thread artifacts, existing docs, and source files. Apply Project Docs Rules.

When creating a new `docs/**` target, also add #.workflow/templates/project_doc.md or #.workflow/templates/architecture_note.md. When updating existing docs, preserve the target file's existing structure.

### Execute A Plan

```text
Mode: execute
Task: build
Plan: .session/threads/{thread}/plan_{topic}.md
```

Add #.workflow/tasks/build.md, the explicit plan, and target artifacts named by the plan.

The plan must state `Compatibility: preserve | breaking` and `Constraint Mode: respect | propose_override | prototype_exception` when compatibility removal or constraint exceptions are involved. `build` must stop on unplanned breaking changes or constraint overrides.

Build output should default to compact `Execution Summary`. Use full summary only for blocked, partial, failed verification, pitfall, scope-expansion risk, or user-requested persistence. The summary is factual, not a review verdict.

In chat, summarize decisions and next steps. Do not paste full artifact contents unless the user asks for a preview.
