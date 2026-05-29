# Copilot Context Guide

Use this file as a manual Add Context menu. Prefer one mode, one task, selected lenses, and only the files needed for the current request.

## Mode Format

```text
Mode: <discuss|persist|execute>
Task: <route|clarify|explore|shape|plan|save|build|review|sync>
Lens: <none|iteration|expand|consistency|distill|language|domain|strategy|redteam|test|architecture|debug>
Artifact: <required for save unless target is explicit>
Status: <inbox|draft|accepted; for save>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference; for save>
Depth: <compact|standard|detailed; optional for save>
Topic: <file-safe topic; for inferred save target>
Target: <optional for save inbox/drafts; required for sync and explicit writes>
Plan: <required for execute; otherwise none>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/lenses/<lens>.md only when selected
- #.workflow/templates/<template>.md only for save or sync in Mode: persist
- #.session/goal/*, relevant .session/inbox/**, relevant .session/drafts/**, relevant .session/accepted/**, docs/**, or source files
Request:
<what you want>
```

## Mode Rules

- `Mode: discuss` is the default. Add the task, selected lenses, and relevant context. Do not add templates. Do not create or update files.
- `Task: save` in `Mode: persist` writes session artifacts to `.session/**`.
- `Task: sync` in `Mode: persist` writes only `docs/**` or explicit `src/**/README.md`.
- `Mode: execute` applies an approved workflow-managed plan through `Task: build`.
- Native Codex/Copilot Plan -> Implement is the `external-agent` write path. It is not a Workflow Mode and does not use `Task: build`.

## Lens Selection Rules

- Default to `Lens: none`.
- Multiple lenses are allowed in `Mode: discuss` only when the user explicitly lists them.
- Copilot may recommend lenses, but must not load or apply them automatically.
- Copilot may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- In multi-lens discuss, organize output in the user's lens order, then provide a converged recommendation and `Save Packet` when worth preserving.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens. If more lenses are needed, split into multiple save steps.

## Write Boundaries

- `discuss`: no writes.
- `save` persist: write `.session/**`.
- `sync` persist: write only `docs/**` or `src/**/README.md`.
- `execute`: may modify broader repository artifacts only when the approved plan says so.
- `external-agent`: native Plan/Implement may write files directly, but the native plan must be audited before implementation and the diff must be reviewed afterward.

## Save Context

Use this when the user says save, write, generate, update, land, or persist a session artifact.

Add:

- #.workflow/tasks/save.md
- the matching artifact template from #.workflow/templates/
- selected #.workflow/lenses/<lens>.md only when named
- `Save Packet`, source discussion, source artifact, or relevant context

Target rules:

- `.session/inbox/**` and `.session/drafts/**` may be inferred from `Artifact + Status + Topic`.
- `.session/accepted/**` requires explicit accepted, approved, or promote intent.
- `.session/goal/**` requires an explicit target.
- Explicit `.session/**` targets should be respected even if naming differs from the recommended prefix.
- `docs/**` or `src/**/README.md` targets route to `sync`.
- Code, `.workflow/**`, `.github/**`, or other repo artifacts route to `build` or external-agent.

Content fidelity:

- `save` should consume `Save Packet` when available.
- Preserve decision-relevant reasoning, not full transcript.
- Keep context, key facts, reasoning trail, rejected options, risks, examples, and next use when they affect later work.
- Use `Depth: detailed` for shape, option, plan, review, decision, distillation, and expanded artifacts unless the user asks for compact output.

## Task Boundary Check

Before acting, classify the request when it is not obviously a fit:

- `fits`: the current task can handle it directly.
- `fits_with_preflight`: the current task can handle it after a read-only preflight.
- `composite`: the request needs multiple tasks.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: required target, approved plan, source of truth, or formal docs safety is missing.

If not `fits`, do not force-fit the request.

## Composite Task Segmentation

Common segmentations:

- Judge current code/docs and decide what to do: `review -> Save Packet -> shape/plan -> Save Packet -> save`.
- Implement a feature from target docs and current code: `plan -> Save Packet -> save draft plan -> review -> Save Packet -> save accepted plan -> external-agent/build -> review`.
- Move discussion into formal docs: `shape/plan/review -> Save Packet -> save accepted -> sync`.
- Distill a reference and improve workflow: `explore --lens distill -> Save Packet -> shape -> Save Packet -> save draft -> plan -> build`.

Use stop points before accepted promotion, implementation, and formal docs sync.

## Template Map For Save

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

### Save Draft Shape

```text
Mode: persist
Task: save
Artifact: shape
Status: draft
Intent: exploration
Depth: detailed
Topic: graph10x_entrypoints
```

Add #.workflow/tasks/save.md and #.workflow/templates/shape.md. Target may be inferred as `.session/drafts/shape_graph10x_entrypoints.md`.

### Save Accepted Decision

```text
Mode: persist
Task: save
Artifact: decision
Status: accepted
Intent: constraint
Depth: detailed
Topic: auth_boundary
```

Add #.workflow/tasks/save.md and #.workflow/templates/decision.md. Accepted intent is explicit.

### Sync Formal Docs

```text
Mode: persist
Task: sync
Target: docs/{area}/{topic}.md
Source:
- .session/accepted/decision_{topic}.md
```

Add #.workflow/tasks/sync.md, #.workflow/templates/sync.md, relevant accepted session artifacts, existing docs, and source files. Apply Formal Docs Rules.

### Execute A Plan

```text
Mode: execute
Task: build
Plan: .session/accepted/plan_{topic}.md
```

Add #.workflow/tasks/build.md, the approved plan, and target artifacts named by the plan.

In chat, summarize decisions and next steps. Do not paste full artifact contents unless the user asks for a preview.
