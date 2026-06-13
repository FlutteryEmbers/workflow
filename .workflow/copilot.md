# Copilot Context Guide

Use this file as a manual Add Context menu. Prefer one mode, one task, selected lenses, and only the files needed for the current request.

## GitHub Prompt Commands

Use dedicated workflow prompt commands for common Copilot work:

- `/wf-route`: choose the smallest useful next path.
- `/wf-clarify`: explain terms, prior answers, statements, assumptions, or request boundaries.
- `/wf-shape`: discuss what-if, option-comparison, concept-level, or direction-setting work.
- `/wf-plan`: produce a planning draft, repo-aware plan, or handoff.
- `/wf-review`: review plans, diffs, docs/code drift, or artifacts.
- `/wf-persist`: write `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**`.
- `/wf-sync`: run the stable-document projection stage for project docs, code-adjacent README, or session archive summaries.

Recommended daily chain:

```text
/wf-clarify -> /wf-shape -> /wf-plan -> /wf-review -> /wf-persist -> /wf-sync
```

Use `workflow-lite.prompt.md` as fallback/router for mixed requests, unclear task boundaries, or full protocol control. Prompt commands are shortcuts only; `.workflow/tasks/**` remains the source of truth.

## Mode Format

```text
Mode: <discuss|persist|execute>
Output: <compact|normal|full>
Task: <route|clarify|explore|shape|plan|persist|build|review|sync>
Lens: <none|consistency|distill|language|domain|redteam|test|architecture|debug>
Artifact: <required for persist unless target is explicit>
Artifact State: <inbox|working|settled|superseded; for persist metadata>
Thread: <thread-name; for persist thread target inference>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference; for persist>
Depth: <compact|standard|detailed; optional for persist>
Topic: <file-safe topic; for inferred persist target>
Sync Domain: <project-docs|session-archive; required for sync when target does not make it obvious>
Target: <optional for persist; required for sync stable documents and explicit writes>
Plan: <required for execute; otherwise none>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/lenses/<lens>.md only when selected
- #.workflow/templates/<template>.md only for persist or sync in Mode: persist
- relevant .session/inbox/**, relevant .session/threads/**, docs/**, or source files
Request:
<what you want>
```

## Mode Rules

- `Mode: discuss` is the default. Add the task, selected lenses, and relevant context. Do not add templates. Do not create or update files.
- `Task: persist` in `Mode: persist` writes active session artifacts to `.session/inbox/**` or `.session/threads/**`.
- `Task: sync` in `Mode: persist` writes only stable-document targets: allowed project docs targets, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.
- `Mode: execute` applies an explicit workflow-managed plan through `Task: build`.
- Native Codex/Copilot Plan -> Implement is the `external-agent` write path. It is not a Workflow Mode and does not use `Task: build`.

## Conversation-to-Artifact Output Flow

Default to `Output: compact`.

Protocol: `Output: compact | normal | full`.

- `compact`: general discussion. Optimize the next turn, not archival completeness.
- `normal`: refine. Prepare key structure and important context for later persist, without writing files.
- `full`: artifact, handoff, audit, diff review, or complex routing.

Recommended flow:

```text
compact discussion -> normal refine -> full persist
```

## External Goal Intake

Use one of two paths:

- Long or reusable external source: persist `Artifact: brief` with `Brief Type: external-goal`, then shape from that inbox brief and persist the shape.
- Current conversational goal: shape directly in chat, then persist the shape if it is worth preserving.

`shape` is the reasoned projection from chat or an inbox goal brief. The durable shaped result belongs in `.session/threads/{thread}/shape_{topic}.md`.

In `Mode: discuss`, do not output a full `Persist Packet` by default. Use `Persist Candidate` instead:

```text
Persist Candidate: Artifact=<artifact>; Thread=<thread>; Topic=<topic>; Suggested Target=<path>
```

Full `Persist Packet` is allowed only when the user asks to persist, says `Output: full`, asks for a handoff/audit, or the current response is the source for a following `Task: persist`.

Compact format:

```text
User Intent: <one line about what the user wants>
Current Read: <optional one line about relevant code/docs/discussion facts>
Take:
- <3-5 bullets max>
Risks/Unknowns:
- <0-3 bullets max>
Next:
- <one suggested next move>
Persist Candidate: <none or one line; candidate only, do not write>
```

Normal refine format:

```text
User Intent: <one line about what the user wants>
Current Read: <optional one line about relevant code/docs/discussion facts>
Refined Direction / Plan:
- <key structure>
Discussion Notes To Preserve:
- <phase, constraints, examples, accepted risks, user corrections>
Open Questions:
- <questions>
Persist Candidate:
- <artifact/thread/topic/target>
```

## Task Boundary Shortcut

When unsure, start with `shape`. Use `clarify` for meaning, `explore` for evidence, and `review` for verdict.

- `clarify = explain/restate/unpack`: terms, prior AI answers, statements, assumptions, scope boundaries, success criteria, or "what does this mean" questions.
- `shape = synthesis`: ambiguous, what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests.
- `explore = evidence`: code/docs/reference/behavior/entrypoint/dependency fact gathering.
- `review = verdict`: existing target reasonableness, readiness, conflict, safety, or acceptance checks.
- `plan = planning sequence`: chosen direction to phases, repo-aware steps, or executable handoff.

Lenses may strengthen the selected task, but must not change task responsibility.

## Discussion Freedom

Workflow Lite is human-in-the-loop first. In `Mode: discuss`, Copilot may be useful before all evidence is complete.

- `shape` may output `Provisional Recommendation`, `Best Guess`, `Candidate Options`, and `What Would Change My Mind`.
- `explore` may output `Candidate Interpretations`, `Likely Entry Points`, and `Borrowable Ideas`.
- `review` may output `Minimal Revision Sketch` and `Repair Direction`.
- `plan` may output a non-build-ready `Planning Draft`.
- Include `Confidence`, `Assumptions`, and `Human Decision Needed` when the output is uncertain or consequential.

Do not treat discussion freedom as write permission. `persist`, `sync`, `build`, and external-agent implementation keep their normal boundaries.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `clarify` for meaning, explanation, restatement, difference, assumptions, hidden scope, or prior AI answer unpacking.
- Use `explore` for what exists, where it is, how it appears to work, and how reliable the evidence is.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.
- Do not add the `consistency` lens for discovery questions.

## Lens Selection Rules

- Default to `Lens: none`.
- Multiple lenses are allowed in `Mode: discuss` only when the user explicitly lists them.
- Copilot may recommend lenses, but must not load or apply them automatically.
- Copilot may suggest `redteam` when risk triggers match, but must not auto-load or apply it.
- In multi-lens discuss, organize output in the user's lens order, then provide a converged recommendation and `Persist Candidate` when worth preserving.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens. If more lenses are needed, split into multiple persist steps.

Use `Abstraction Level: concept | phase-plan | implementation-plan` to separate direction, staged planning, and execution handoff. `shape` normally produces `concept` and records `Impact Surface` plus `Recommended Next Abstraction Level` when it may feed planning. `plan` automatically chooses `phase-plan` or `implementation-plan` unless the user explicitly names one; inherit the shape recommendation when present, otherwise infer from the request and read-only preflight, defaulting to `phase-plan` when uncertain. Option comparison is built into `shape`; abstraction level is core protocol.

## Write Boundaries

- `discuss`: no writes.
- `persist` persist: write `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**` disposable exploration notes.
- `sync` persist: write only stable-document targets for `Sync Domain: project-docs | session-archive`.
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
- `Persist Candidate`, `Persist Packet`, source discussion, source artifact, or relevant context

Target rules:

- `.session/inbox/**` may be inferred from `Artifact State: inbox`; `.session/threads/{thread}/{artifact}_{topic}.md` may be inferred from explicit `Thread + Artifact + Topic` or automatic same-work-item fit.
- `Target Directory` may be used to choose a specific thread folder.
- Thread directories are small closable work items. Reuse active threads for continuations, refinements, corrections, review responses, or implementation follow-ups for the same work item.
- Create or suggest a new thread for a distinct bounded decision, change, or question. Recency alone is not enough to reuse a thread.
- Use related old or closed threads as `Source Context` unless they pass the same-work-item test.
- Explicit active `.session/inbox/**` and `.session/threads/**` targets should be respected even if naming differs from the recommended prefix.
- `.session/archive/**` targets route to `sync` with `Sync Domain: session-archive`.
- Explicit `notes/**` targets are allowed for disposable exploration notes. Never infer `notes/**`.
- `docs/**` or `src/**/README.md` targets route to `sync` with `Sync Domain: project-docs`.
- Code, `.workflow/**`, `.github/**`, or other repo artifacts route to `build` or external-agent.

Content fidelity:

- `persist` should consume `Persist Packet` when available, but it may also consume `Persist Candidate` plus recent discussion.
- Use `persist shape_<topic>` to reference a shape by `Artifact ID`; this anchors source context and does not derive the thread directory.
- Include `Thread Inference Note` when the inferred target depends on assumptions or low-confidence same-work-item fit.
- Preserve decision-relevant reasoning, not full transcript.
- Keep context, key facts, decision trail, rejected options, risks, examples, and next use when they affect later work.
- Use artifact `Depth: detailed` for shape, option, plan, review, decision, distillation, and expanded artifacts unless the user asks for compact artifact output.
- For `notes/**`, compact or standard depth is enough unless the user asks for more detail.

Revision boundary:

- `persist` may restructure an artifact and apply explicit review edits.
- `persist` must not choose a new direction, re-plan execution, judge whether review feedback is correct, or promote unclear `needs changes` content.
- If revision needs new judgment, route back to `shape`, `plan`, or `review`.

Exploration notes:

- `notes/**` is disposable exploration memory, not project docs.
- `notes/**` is not an execution source for build or external-agent implementation.
- Settled conclusions should later be persisted to `.session/threads/**` or synced to `docs/**`.
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

- Judge current code/docs and decide what to do: `review -> Persist Candidate -> shape/plan -> persist`.
- Implement a feature from target docs and current code: `plan -> Persist Candidate -> persist thread plan -> review -> external-agent/build -> review`.
- Move settled thread conclusions into project docs: `shape/plan/review -> Persist Candidate -> persist thread artifact -> sync`.
- Distill a reference and improve workflow: `explore --lens distill -> Persist Candidate -> shape -> persist thread artifact -> plan -> build`.
- Ambiguous what-if or entrypoint selection: `shape`, then `explore -> shape` only if missing evidence could change the recommendation.
- Understand code/docs mismatches as discovery: `explore -> Persist Candidate -> persist note`, with `Reliability Notes`; use `review --lens consistency` only for source-of-truth judgments.

Use stop points before implementation and stable-document sync.

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
Artifact State: working
Intent: exploration
Depth: detailed
Thread: workflow-thread-naming
Topic: thread_inference
```

Add #.workflow/tasks/persist.md and #.workflow/templates/shape.md. Target may be inferred as `.session/threads/workflow-thread-naming/shape_thread_inference.md`.
You may also say `persist shape_thread_inference` to anchor the persist request to the shape artifact; thread selection still follows same-work-item fit.

### Persist Exploration Note

```text
Mode: persist
Task: persist
Artifact: note
Artifact State: inbox
Intent: exploration
Depth: compact | standard
Target: notes/thread-naming.md
```

Add #.workflow/tasks/persist.md and #.workflow/templates/note.md. `notes/**` must be explicit and remains disposable exploration memory.

### Persist Thread Decision

```text
Mode: persist
Task: persist
Artifact: decision
Artifact State: settled
Intent: constraint
Depth: detailed
Thread: workflow-goal-removal
Topic: goal_boundary
```

Add #.workflow/tasks/persist.md and #.workflow/templates/decision.md.

### Sync Stable Documents

```text
Mode: persist
Task: sync
Sync Domain: project-docs
Sync Object: architecture
Scope: auth
Source Of Truth: .session/threads/{thread}/decision_{topic}.md
Target: docs/architecture.md
Source:
- .session/threads/{thread}/decision_{topic}.md
```

Add #.workflow/tasks/sync.md, #.workflow/templates/sync.md, relevant thread artifacts, existing docs, and source files. Apply Project Docs Rules for `project-docs` or Archive Rules for `session-archive`.

When creating a new `architecture`, `feature`, or `reference` docs target, also add #.workflow/templates/project_doc.md. When updating existing docs, preserve the target file's existing structure.

Use `Sync Object: architecture | feature | reference | code-readme | archive-summary | all`. `Target` wins, `Target Directory` may guide creation/update, and existing docs convention may guide suggested targets. Do not create workflow-internal docs such as `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares a host-project taxonomy override.

For `session-archive`, use target `.session/archive/<thread>/summary.md` and add #.workflow/templates/archive_summary.md. Require Source Thread, Thread Status, Archive Purpose, Summary Scope, and Next Retrieval Use. Do not edit active `.session/threads/**`.

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
