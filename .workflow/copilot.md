# Copilot Context Guide

Use this file as a manual Add Context menu. Prefer one mode, one task, selected lenses, and only the files needed for the current request.

## GitHub Prompt Commands

Use dedicated workflow prompt commands for common Copilot work:

- `/wf-route`: choose the smallest useful next path.
- `/wf-clarify`: explain terms, prior answers, statements, assumptions, or request boundaries.
- `/wf-explore`: extract read-only evidence from code, docs, behavior, dependencies, or references.
- `/wf-distill`: summarize or distill specified files, folders, threads, discussion, docs, or references.
- `/wf-shape`: discuss what-if, option-comparison, concept-level, or direction-setting work.
- `/wf-plan`: produce a repo-aware plan draft or handoff based on input sufficiency.
- `/wf-pplan`: produce a frozen plan draft, then review it for intended next use.
- `/wf-review`: review plans, diffs, docs/code drift, or artifacts.
- `/wf-persist`: write `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**`.
- `/wf-build`: execute an explicit workflow-managed plan with bounded execution and `Execution Trace`.
- `/wf-sync`: run the stable-document projection stage for project docs, code-adjacent README, or session archive summaries.

Recommended daily chain:

```text
/wf-clarify -> /wf-explore or /wf-distill -> /wf-shape
-> (/wf-plan -> /wf-review | /wf-pplan)
-> /wf-persist -> /wf-build -> /wf-review (diff review) -> /wf-sync
```

Use `workflow-lite.prompt.md` as fallback/router for mixed requests, unclear task boundaries, or full protocol control. Prompt commands are shortcuts only; `.workflow/tasks/**` remains the source of truth.

## Mode Format

```text
Mode: <discuss|persist|execute>
Task: <route|clarify|explore|distill|shape|plan|persist|build|review|sync>
Lens: <none|consistency|boundary|language|domain|redteam|test|architecture|debug|expert|ponytail>
Artifact: <required for persist unless target is explicit>
Artifact State: <inbox|working|settled|superseded; for persist metadata>
Thread: <thread-name; for persist thread target inference>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference|capture; for persist>
Depth: <compact|standard|detailed; optional for persist>
Topic: <file-safe topic; for inferred persist target>
Sync Domain: <project-docs|session-archive; required for sync when target does not make it obvious>
Target: <optional for persist; required for sync stable documents and explicit writes>
Plan: <required for execute; otherwise none>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/lenses/<lens>.md only when selected
- #.workflow/templates/<template>.md plus the matching metadata partial only for persist or sync in Mode: persist
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

## Standard Response Contract

Copilot exposes no response-level selector. Every task uses one response with
these visible groups in order:

1. `User Intent`
2. `Task State`
3. `Primary Result`
4. `Supporting Information`
5. `Next`
6. `Persistence`

Keep task-specific fields and conditions from `.workflow/tasks/**` inside those
groups. Omit optional empty groups. A request for more detail expands the same
response; it does not load templates or switch modes.

Discussion tasks produce `Persist Candidate` only when the result is worth
saving. They never construct an intermediate artifact packet. `persist`
consumes an explicit source/path, Artifact ID, Persist Candidate,
same-work-item artifacts, or matching recent discussion and user corrections,
then loads the corresponding template and persist metadata partial.

For `Task: plan`, Task State owns `Input Sufficiency`, conditional `Input
Gaps`, `Planning Continuation`, and `Compatibility Intake`. Primary Result
owns `Shape Summary`, `Plan At A Glance`, and `Plan`. Supporting
Information owns impact, scope, verification, compatibility/constraints, risk,
and stop conditions. A `sufficient-for-handoff` Plan is directly usable as an
explicit handoff; persist it when a durable handoff is needed.

Default plan verification remains minimum viable verification: prefer existing
fixture/unit/static/smoke/targeted checks, repo scripts, prompt/static
assertions, or manual acceptance checks. Old baseline, contract freeze, parity
matrix, full regression, and e2e belong to `Lens: test` or explicit
higher-assurance requests.

Recommended flow:

```text
standard discussion -> optional persist candidate -> persist with template
```

## External Goal Intake

Use one of two paths:

- Long or reusable external source: persist `Artifact: brief` with `Brief Type: external-goal`, then shape from that inbox brief and persist the shape.
- Current conversational goal: shape directly in chat, then persist the shape if it is worth preserving.

`shape` is the reasoned projection from chat or an inbox goal brief. The
durable shaped result belongs in
`.session/threads/{thread}/shape_{topic}.md`.

## Task Boundary Shortcut

When unsure, start with `shape`. Use `clarify` for semantic unpacking, `explore` for descriptive inquiry and non-mutating probes, `distill` for user-directed summaries, and `review` for verdict.

- `clarify = semantic unpacking`: terms, prior AI answers, statements, assumptions, scope boundaries, success criteria, or "what does this mean" questions.
- `shape = synthesis`: small discussion fallback for ambiguous, what-if, option-comparison, concept-level, direction-setting, entrypoint-selection, "how should I think about this", or next-step selection requests.
- `explore = descriptive inquiry / observed system map`: how code/docs/behavior work, what exists, where things appear, observed differences, checked-scope evidence, reliability/not-checked notes, downstream use, and temporary non-mutating probes.
- `distill = summary`: user-selected files, folders, threads, docs, discussion, or reference material summarized with observed, inferred, and unknown content separated.
- `review = verdict / gap-analysis`: existing target reasonableness, readiness, conflict, safety, acceptance checks, or baseline gap review.
- `plan = planning sequence`: chosen direction to phases, repo-aware steps, or executable handoff.

Lenses may strengthen the selected task, but must not change task responsibility, write permission, execute permission, or sync permission. Do not use a lens as a skip mechanism.

Task boundary layers:

- `Core Responsibility`: the task's main job.
- `Adjacent Allowance`: small neighboring outputs allowed only when they support the core responsibility.
- `Hard Authority Boundaries`: boundaries the task must not cross.

Prefer Output Shape over prohibition. User-selected task is respected; authority is not expanded. Use `Scope Interpretation` when a request partially fits another output shape.

For `shape`, adjacent allowance includes lightweight clarification, lightweight current-context compression, candidate evidence needs, risk sketch, and non-executable planning sketch. It must route away for formal evidence extraction, specified-source summary, formal verdict, source-of-truth judgment, explicit executable plan candidate, stable sync, writes, execution, or implementation.

Discussion adjacency is allowed; authority is not. Adjacent output may make the current task actionable or recommend a next task, but write, sync, execute, implementation, source-of-truth, and build authority still come only from `Mode`, `Task`, target rules, explicit prerequisites, and explicit executable plans.

Use `boundary` for ownership, dependency direction, contract leakage, provider/package boundaries, provider-owned capability business, main-system business, and migration ownership. Use `boundary, consistency` together when that boundary judgment also depends on source-of-truth, docs/code drift, contract/implementation alignment, or artifact alignment.

Use `ponytail` only when explicitly selected for `shape`, `plan`, or `review`. It applies a demo-first PoC posture with controlled inputs, deliberately relaxed production validation, explicit deferred work, and upgrade triggers. It is not available to `build`, does not persist across requests, and must not activate from PoC/demo wording alone.

## Discussion Advisory Principle

Workflow Lite is human-in-the-loop first. In `Mode: discuss`, Copilot may be useful before all evidence is complete.

- `clarify` may output a lightweight next-task hint.
- `explore` may output `Explore Frame`, `Observed Answer`, `Evidence Basis`, `Evidence Probes`, `Reliability / Not Checked`, `Evidence Sufficiency`, `Downstream Use`, `Follow-up Targets`, `Candidate Review Targets`, and recommended next task.
- `distill` may output `Observed`, `Inferred`, `Unknown`, `Next Use`, `Persist Candidate: Artifact=distillation`, and review/sync suggestion.
- `shape` starts with `Need For Shape`, follows with `Shape Continuation`, and may output `Provisional Recommendation`, `Best Guess`, `Candidate Options`, `What Would Change This`, and allowed lightweight adjacent output when `Boundary Advice` shows a safe fallback response.
- `review` starts non-trivial output with `Review Frame` and includes `Review Verdict`, `Confidence`, `Readiness`, `Blocking Gaps`, `Non-blocking Gaps`, `Can Use For Intended Next Use`, `Recommended Action`, `Suggested Critique`, and `Recommended Next Task`; it may also output `Minimal Revision Sketch` and `Repair Direction`.
- `plan` may output `Input Sufficiency`, `Input Gaps` and `Planning Continuation` when insufficient, a triggered `Compatibility Intake`, minimum viable verification, fallback verification, residual risk, compatibility/constraint plan, and recommended next task.
- `review` may output `Review Frame` fields, `Gap Analysis`, severity, blocking gaps, non-blocking gaps, and conditional `Change Assessment` when the user asks whether something should change or is worth changing.
- Include `Confidence`, `Assumptions`, and `Human Decision State` when the output is uncertain or consequential.
- In `shape`, `Need For Shape` is an advisory continuation classifier; it is not a review verdict or hard gate. Put it after current read and before `Shape Continuation`. Use `Human Decision State` after `Shape Continuation` when a user-owned choice matters. If state is `checkpoint`, use native user-input UI when available or output structured `User Checkpoint` and wait. If state is `unresolved`, continue with provisional direction, assumptions, what would change it, and advisory next task.

Do not treat discussion freedom as write permission. `persist`, `sync`, `build`, and external-agent implementation keep their normal boundaries.

## Copilot Native Question UI

Use `vscode/askQuestions` as the Copilot renderer for exactly two protocol
checkpoints:

- `shape` `User Checkpoint`, only when `Need For Shape Status: needs-direction` and `Human Decision State: checkpoint`. Ask one consequential choice with 2-3 mutually exclusive options; preserve label, explanation, and risk; put the recommended option first and mark it `(Recommended)`. Wait before finalizing `Take`, `Provisional Recommendation`, `Impact Surface`, or `Persist Candidate`.
- `plan` `Compatibility Intake`, only after repo preflight satisfies every trigger in `.workflow/tasks/plan.md`. In one UI round, ask consumer scope and data/config lifecycle, plus cutover style only when external consumers, persisted data, or material transition cost were found. Each question has 2-3 mutually exclusive options with impact descriptions; put the recommendation first and mark it `(Recommended)`. When the third question is omitted, use the preflight-established atomic cutover for mapping. Continue with the complete Plan only after answers are available.

Do not use native questions for fact discovery, ordinary clarification, review
verdicts, technical solution delegation, target selection, task routing, repo
preflight, or write/sync/build authorization. `Compatibility Intake` does not use
`Human Decision State` and owns no direction, source-of-truth, or architecture
choice. If native UI is unavailable, output the applicable structured checkpoint
and wait. An unresolved Compatibility Intake must report `Input Sufficiency:
insufficient` and `Input Gaps: compatibility policy`, and must not output a Plan
body or handoff packet.

For `/wf-pplan`, resolve any triggered Compatibility Intake before creating or
freezing the Plan Draft. If it remains unanswered, do not freeze a draft or run
Plan Review. After answers, continue in `plan -> freeze -> review` order.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `clarify` for meaning, explanation, restatement, difference, assumptions, hidden scope, or prior AI answer unpacking.
- Use `explore` for what exists, where it is, how it appears to work, source-backed observed differences, checked-scope evidence, and evidence reliability.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth. Use `Change Assessment` only for change-seeking review questions; route "if changing, what directions exist" to `shape`, and "how to make the chosen change" to `plan`.
- Do not add the `consistency` lens for discovery questions.

`explore` is descriptive inquiry for shape and review. Use `explore -> plan` only when the direction or target is already selected and the evidence only fills repo-aware planning context. Shape/review/plan may perform bounded evidence checks only to support their own output shape; if descriptive source inquiry becomes the main deliverable, route to `explore`.

## Lens Selection Rules

- Default to `Lens: none`.
- Multiple lenses are allowed in `Mode: discuss` only when the user explicitly lists them.
- Copilot may recommend lenses, but must not load or apply them automatically.
- Copilot may suggest an explicit redteam critique when the user asks for critique or an existing target has costly failure paths, but must not load or apply it automatically.
- Embedded critique is lightweight core behavior in `shape`, `plan`, and `build`; it names risks and stop conditions without loading the redteam lens or issuing review verdicts.
- In multi-lens discuss, organize output in the user's lens order, then provide a converged recommendation and `Persist Candidate` when worth preserving.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens. If more lenses are needed, split into multiple persist steps.

Use `Input Sufficiency: insufficient | sufficient-for-draft | sufficient-for-handoff` to classify whether source input supports plan output. `sufficient-for-handoff` is not a review verdict or execution authorization. `shape` stays at concept level and uses `Need For Shape` as advisory continuation context. `plan` outputs input sufficiency, `Planning Continuation` when insufficient, a structured `Compatibility Intake` when its narrow trigger passes and native UI is unavailable, plan content when sufficient, and minimum viable verification; `review` owns `Review Frame`, formal `Blocking Gaps`, severity, plan usability verdicts, `Can Use For Intended Next Use`, and change necessity judgment.

For plan reviews, `Review Verdict: ready` means no blocking gaps for `Intended Next Use`. Do not block a plan for optional improvement only.

## Write Boundaries

- `discuss`: no writes.
- `persist` persist: write `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**` disposable exploration notes.
- `sync` persist: write only stable-document targets for `Sync Domain: project-docs | session-archive`.
- `execute`: may modify broader repository artifacts only when the explicit plan says so.
- `external-agent`: native Plan/Implement may write files directly; plan review before implementation and diff review afterward are recommended risk controls.
- `build` outputs an `Execution Trace`; it does not write `.session/**`. Persist current-work-item audit output with `Task: persist`, `Artifact: note`, and `Intent: audit`; persist reusable execution discoveries as inbox notes with `Intent: capture`.

## Compatibility / Constraint Policy

- Default to `Compatibility: preserve` and `Constraint Mode: respect`.
- `shape` may suggest `consider breaking` or `consider override`, but must not activate it unless the user explicitly requested it.
- `plan` runs compatibility preflight before asking. Trigger `Compatibility Intake` only when policy is unlocked, a real compatibility surface exists, preserve/breaking materially changes scope or verification, and repository evidence cannot determine consumer scope, data lifetime, or transition tolerance. Ask two questions by default and a third cutover question only for external consumers, persisted data, or material transition cost.
- Map controlled + disposable + atomic to `breaking`; migrate-then-cutover to `breaking` with migration and old-read/write stop points; temporary bridge to current `preserve` with a removal trigger and later breaking work; unknown consumers, concurrent formats, or long-term retention to `preserve`. `ponytail` may influence the recommendation for controlled/disposable evidence but never selects breaking automatically.
- `plan` must encode any explicit breaking or constraint exception into removed compatibility, migration/alias, do-not-preserve, cleanup, and stop conditions.
- `build` may execute breaking changes or constraint exceptions only when the plan explicitly allows them.
- Use `Constraint Mode: prototype_exception` only for temporary PoC scope; do not treat it as durable project docs content until confirmed.

## Persist Context

Use this when the user says persist, write, generate, update, land, or record a session artifact.

Add:

- #.workflow/tasks/persist.md
- the matching artifact template from #.workflow/templates/ and #.workflow/templates/_persist_metadata.md
- selected #.workflow/lenses/<lens>.md only when named
- `Persist Candidate`, explicit source, source discussion, source artifact, or relevant context

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

- `persist` consumes explicit sources first, then Artifact ID, `Persist Candidate`, same-work-item artifacts, and matching recent discussion plus user corrections.
- Use `persist shape_<topic>` to reference a shape by `Artifact ID`; this anchors source context and does not derive the thread directory.
- Include `Thread Inference Note` when the inferred target depends on assumptions or low-confidence same-work-item fit.
- Preserve decision-relevant reasoning, not full transcript.
- Keep context, key facts, decision trail, rejected options, risks, examples, and next use when they affect later work.
- Use artifact `Depth: detailed` for shape, plan, review, and distillation artifacts unless the user asks for compact artifact output.
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
- `fallback_fit`: no task fits exactly, but the selected task can handle the primary user intent with only allowed adjacent output.
- `composite`: the request needs multiple tasks.
- `wrong_task`: another task is the proper entrypoint; in discussion, still provide useful in-shape output when safe and include `Boundary Advice`.
- `missing_prerequisite`: required target, explicit plan, source of truth, or project docs safety is missing.

If not `fits` or `fallback_fit`, do not force-fit the request.

## Composite Task Segmentation

Common segmentations:

- Judge current code/docs and decide what to do: `review -> Persist Candidate -> shape/plan -> persist`.
- Implement a feature from target docs and current code: `plan -> Persist Candidate -> persist thread plan -> optional review -> external-agent/build -> recommended review`.
- Move settled thread conclusions into project docs: `shape/plan/review -> Persist Candidate -> persist thread artifact -> sync`.
- Distill a reference and improve workflow: `distill -> Persist Candidate -> shape -> persist thread artifact -> plan -> build`.
- Ambiguous what-if or entrypoint selection: `shape`, then `explore -> shape` only if missing evidence could change the recommendation.
- Evidence to verdict/gap: `explore -> review`.
- Evidence to plan: `explore -> plan` only when direction or target is already selected.
- Understand code/docs differences as discovery: `explore -> Persist Candidate -> persist note`, with `Reliability / Not Checked`; use `review --lens consistency` only for source-of-truth judgments.
- Provider/contract/package boundary: `shape --lens boundary` for boundary model, `explore --lens boundary` for imports/call evidence, `review --lens boundary` for ownership verdict, or `review --lens boundary, consistency` when source-of-truth or docs/code alignment also matters.

Use handoff points before implementation and stable-document sync.

## Template Map For Persist

- `Artifact: brief`: #.workflow/templates/brief.md
- `Artifact: note`: #.workflow/templates/note.md
- `Artifact: shape`: #.workflow/templates/shape.md
- `Artifact: plan`: #.workflow/templates/plan.md
- `Artifact: review`: #.workflow/templates/review.md
- `Artifact: distillation`: #.workflow/templates/distillation.md

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

### Persist Thread Direction

```text
Mode: persist
Task: persist
Artifact: shape
Artifact State: settled
Intent: constraint
Depth: detailed
Thread: workflow-goal-removal
Topic: goal_boundary
```

Add #.workflow/tasks/persist.md and #.workflow/templates/shape.md.

### Sync Stable Documents

```text
Mode: persist
Task: sync
Sync Domain: project-docs
Sync Object: architecture
Scope: auth
Source Of Truth: .session/threads/{thread}/shape_{topic}.md
Target: docs/architecture.md
Source:
- .session/threads/{thread}/shape_{topic}.md
```

Add #.workflow/tasks/sync.md, #.workflow/templates/_sync_metadata.md, relevant thread artifacts, existing docs, and source files. Apply Project Docs Rules for `project-docs` or Archive Rules for `session-archive`.

When creating a new `architecture`, `feature`, or `reference` docs target, also add #.workflow/templates/project_doc.md. When updating existing docs, preserve the target file's existing structure and refresh sync metadata.

Use `Sync Object: architecture | feature | reference | code-readme | archive-summary | all`. `Target` wins, `Target Directory` may guide creation/update, and existing docs convention may guide suggested targets. Do not create workflow-internal docs such as `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares a host-project taxonomy override.

For `session-archive`, use target `.session/archive/<thread>/summary.md` and add #.workflow/templates/archive_summary.md plus #.workflow/templates/_sync_metadata.md. Require Source Thread, Thread Status, Archive Purpose, Summary Scope, and Next Retrieval Use. Do not edit active `.session/threads/**`.

### Execute A Plan

```text
Mode: execute
Task: build
Plan: .session/threads/{thread}/plan_{topic}.md
```

Add #.workflow/tasks/build.md, the explicit plan, and target artifacts named by the plan.

The plan must state `Compatibility: preserve | breaking` and `Constraint Mode: respect | propose_override | prototype_exception` when compatibility removal or constraint exceptions are involved. `build` must stop on unplanned breaking changes or constraint overrides.

Build must establish `Execution Environment Contract` and command provenance before verification: CWD, repo root, OS/shell, package manager or runner, available scripts, command source, and retry budget. Do not blindly retry path, cwd, shell, quoting, or command variants; default retry budget is 2 for the same failure class.

Build output uses a concise `Execution Trace` for ordinary successful execution and automatically adds expanded evidence for blocked, partial, failed verification, pitfall, reusable execution discovery, scope-expansion risk, or user-requested persistence. The trace is factual, not a review verdict.

In chat, summarize decisions and next steps. Do not paste full artifact contents unless the user asks for a preview.
