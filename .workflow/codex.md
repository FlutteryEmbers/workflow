# Codex Adapter

Codex support is a manual adapter. It does not add `AGENTS.md`, does not replace `.workflow/tasks/**`, and does not create a separate Codex workflow. This repo also includes an optional `workflow-lite-shortcuts` skill source for short task phrases; the skill is a shortcut layer only.

Use this file when you want Codex to follow Workflow Lite explicitly. Add only the task, selected lenses, templates, session artifacts, project docs, and source files needed for the current request.

## Core Rules

- `.workflow/README.md` is the workflow source of truth.
- Optional shortcut skill source: `skills/workflow-lite-shortcuts/`. Install it into `$CODEX_HOME/skills` or `~/.codex/skills` only when you want phrases like `wf shape`, `wf plan`, `wf pplan`, or `wf build` to map to the matching Workflow Lite context.
- Default to `Mode: discuss`.
- Use one standard response with visible groups in this order: `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`. Preserve each task's field names and conditions; omit optional empty groups.
- For `Task: plan`, Task State starts with `Input Sufficiency`; Primary Result contains `Shape Summary` with `Motivation`, `Plan At A Glance`, and `Plan` when allowed; Supporting Information contains impact, scope, verification, compatibility/constraints, risk, and stop conditions. Use `Shape Summary: Source=chat` when there is no persisted shape artifact; use `Motivation: unknown` rather than inventing.
- Default plan verification is minimum viable verification: prefer existing fixture/unit/static/smoke/targeted checks, repo scripts, prompt/static assertions, or manual acceptance checks over ideal high-assurance test systems. Old baseline, contract freeze, parity matrix, full regression, and e2e belong to `Lens: test` or explicit higher-assurance requests, not default plan prerequisites.
- Treat `sufficient-for-handoff` as the completeness control for implementation or external-agent handoff. The current Plan may be used as an explicit handoff; persist it for durable handoff. `Depth: detailed` is persisted artifact metadata, not a chat response mode.
- When unsure, start with `shape`. Use `explore` for evidence, `distill` for user-directed summaries, and `review` for verdict.
- Do not load all tasks, lenses, templates, or `.workflow/**` by default.
- Use one task as the main workflow context.
- Load lenses only when the user explicitly selects them.
- Use `ponytail` only when explicitly selected for `shape`, `plan`, or `review`. It applies a demo-first PoC posture with controlled inputs, deliberately relaxed production validation, explicit deferred work, and upgrade triggers; it is not available to `build` and does not activate from PoC/demo wording alone.
- Use `Input Sufficiency` rules when the user wants planning, implementation handoff, or strong-model-to-weak-model handoff.
- Codex may suggest an explicit redteam critique when the user asks for critique or an existing target has costly failure paths, but must not load or apply it automatically.
- Embedded critique is lightweight core behavior in `shape`, `plan`, and `build`; it names risks and stop conditions without loading the redteam lens or issuing review verdicts.
- Load templates only for `persist` or `sync` in `Mode: persist`; discussion tasks must not copy final artifact templates.
- Discussion tasks output only `Persist Candidate` when worth saving. For `persist`, load only the matching template for the selected artifact, apply `.workflow/templates/_persist_metadata.md`, and write every required template section.
- Treat `.session/**` as working memory, not project source of truth.
- Treat `.session/threads/**` as session working memory grouped by small closable work item.
- Use explicit `.session/threads/{thread}/plan_{topic}.md` files for workflow-managed build input.
- Treat explicit `notes/**` targets as disposable exploration notes, not project docs or execution sources.
- Treat `docs/**` as code-aligned project docs and apply Project Docs Rules before writing.
- Lenses may strengthen the selected task, but must not change task responsibility, write permission, execute permission, or sync permission. Do not use a lens as a skip mechanism.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`; breaking compatibility or constraint exceptions require explicit user or explicit-source intent.

## Task Boundary Shortcut

- `shape = synthesis`: small discussion fallback for ambiguous, what-if, option-comparison, concept-level, direction-setting, entrypoint-selection, "how should I think about this", or next-step selection requests.
- `explore = descriptive inquiry / observed system map`: how code/docs/behavior work, what exists, where things appear, observed differences, checked-scope evidence, reliability/not-checked notes, downstream use, and temporary non-mutating probes.
- `distill = summary`: user-directed summary, folder summary, source distillation, or archive-summary draft for specified source material.
- `review = verdict / gap-analysis`: existing target reasonableness, readiness, conflict, safety, acceptance checks, or baseline gap review.
- `plan = planning sequence`: chosen direction to phases, repo-aware steps, or executable handoff.

Task boundary layers:

- `Core Responsibility`: the task's main job.
- `Adjacent Allowance`: small neighboring outputs allowed only when they support the core responsibility.
- `Hard Authority Boundaries`: boundaries the task must not cross.

Prefer Output Shape over prohibition. User-selected task is respected; authority is not expanded. Use `Scope Interpretation` when a request partially fits another output shape.

For `shape`, adjacent allowance includes lightweight clarification, lightweight current-context compression, candidate evidence needs, risk sketch, and non-executable planning sketch. It must route away for formal evidence extraction, specified-source summary, formal verdict, source-of-truth judgment, explicit executable plan candidate, stable sync, writes, execution, or implementation.

Discussion adjacency is allowed; authority is not. Adjacent output may make the current task actionable or recommend a next task, but write, sync, execute, implementation, source-of-truth, and build authority still come only from `Mode`, `Task`, target rules, explicit prerequisites, and explicit executable plans.

Use `boundary` for ownership, dependency direction, contract leakage, provider/package boundaries, provider-owned capability business, main-system business, and migration ownership. Use `boundary, consistency` together when that boundary judgment also depends on source-of-truth, docs/code drift, contract/implementation alignment, or artifact alignment.

Use `ponytail` for an explicitly selected demo-first PoC posture. In `shape`, define the smallest credible demo path and controlled assumptions. In `plan`, encode relaxed validation, do-not-add constraints, minimum proof, deferred work, and upgrade triggers into the existing plan fields. In `review`, judge speculative fields, validation, dependencies, abstractions, and compatibility mechanisms against the declared Demo Contract. Do not apply it automatically or pass it to `build`.

## Discussion Advisory Principle

Workflow Lite is human-in-the-loop first. In `Mode: discuss`, Codex may provide useful provisional thinking without treating it as approval.

- `clarify`: lightweight next-task hint.
- `explore`: `Explore Frame`, `Observed Answer`, `Evidence Basis`, `Evidence Probes`, `Reliability / Not Checked`, `Evidence Sufficiency`, `Downstream Use`, `Follow-up Targets`, `Candidate Review Targets`, recommended next task.
- `distill`: `Next Use`, `Persist Candidate`, review suggestion, or sync/archive handoff hint.
- `shape`: `Need For Shape`, `Shape Continuation`, `Provisional Recommendation`, `Best Guess`, `Candidate Options`, `What Would Change This`, and allowed lightweight adjacent output when `Boundary Advice` shows a safe fallback response.
- `review`: `Review Frame`, `Review Verdict`, `Confidence`, `Readiness`, `Blocking Gaps`, `Non-blocking Gaps`, `Can Use For Intended Next Use`, `Recommended Action`, `Suggested Critique`, `Recommended Next Task`, optional `Gap Analysis`, `Minimal Revision Sketch`, `Repair Direction`, and conditional `Change Assessment`.
- `plan`: `Input Sufficiency`, `Input Gaps` and `Planning Continuation` when insufficient, a triggered `Compatibility Intake`, minimum viable verification, fallback verification, residual risk, compatibility/constraint plan, and recommended next task.
- Review plans under `verdict-review` with `Review Target Kind: plan` and explicit `Intended Next Use`; use `gap-analysis` when the plan asks review to diagnose a system or protocol gap against a baseline.
- Add `Confidence`, `Assumptions`, and `Human Decision State` when uncertainty or impact is material.
- In `shape`, `Need For Shape` is an advisory continuation classifier; it is not a review verdict or hard gate. Put it after current read and before `Shape Continuation`. Use `Human Decision State` after `Shape Continuation` when a user-owned choice matters. If state is `checkpoint`, use native user-input UI when available or output structured `User Checkpoint` and wait. If state is `unresolved`, continue with provisional direction, assumptions, what would change it, and advisory next task.

Discussion freedom does not permit file writes, stable-document sync, implementation, or source-of-truth decisions.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `explore` for what exists, where it is, how it appears to work, source-backed observed differences, checked-scope evidence, and evidence reliability.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth. Use `Change Assessment` only for change-seeking review questions; route "if changing, what directions exist" to `shape`, and "how to make the chosen change" to `plan`.
- Do not add the `consistency` lens for discovery questions.

`explore` is descriptive inquiry for shape and review. Use `explore -> plan` only when the direction or target is already selected and the evidence only fills repo-aware planning context. Shape/review/plan may perform bounded evidence checks only to support their own output shape; if descriptive source inquiry becomes the main deliverable, route to `explore`.

## Compatibility / Constraint Policy

- `shape` may identify compatibility pressure and constraint tension, but must not switch to `Compatibility: breaking` or `Constraint Mode != respect` automatically.
- `plan` runs repo preflight before asking and may use only the compatibility-specific `Compatibility Intake` from `.workflow/tasks/plan.md`; ordinary Plan questions remain forbidden. Trigger it only when policy is unlocked, a real compatibility surface exists, preserve/breaking materially changes scope or verification, and repo evidence cannot determine consumer scope, data lifetime, or transition tolerance.
- Ask consumer scope and data/config lifecycle in one intake round by default; add cutover style only for external consumers, persisted data, or material transition cost. Each question has 2-3 mutually exclusive options with the recommended option first. When the third question is omitted, use the preflight-established atomic cutover for mapping. `ponytail` may favor demo-only, rebuild, and atomic cutover when evidence is controlled/disposable, but does not select breaking.
- Use native choice UI when available. Otherwise output `Input Sufficiency: insufficient`, `Input Gaps: compatibility policy`, and the structured `Compatibility Intake`, then wait without a Plan body or handoff packet. After answers, map controlled + disposable + atomic and migrate-then-cutover to `breaking`; map temporary bridges to current `preserve` with a removal trigger; map unknown consumers, concurrent formats, or long-term retention to `preserve`.
- For composite `pplan`, resolve the intake before generating or freezing the Plan Draft. If unanswered, do not freeze a draft or run Review; after answers, continue in `plan -> freeze -> review` order.
- `plan` must name removed compatibility, migration/alias decisions, constraint exceptions, do-not-preserve items, cleanup, and stop conditions when breaking or exception mode is selected.
- `build` or native implement must stop if it needs unplanned compatibility removal or constraint override.
- `prototype_exception` is temporary PoC scope, not a durable project constraint.

## Planning Input Sufficiency

Use `Input Sufficiency: insufficient | sufficient-for-draft | sufficient-for-handoff` to separate missing input, discussion/review drafts, and handoff-grade plans. This classifies source input, not generated plan quality.

- `shape` produces concept-level direction or continuation and may recommend `plan` or `review`; it uses `Need For Shape` as advisory continuation context.
- `plan` outputs `Input Sufficiency`, `Input Gaps` and `Planning Continuation` when insufficient, and a plan body plus minimum viable verification only when input is sufficient.
- `Input Sufficiency` is not a gate verdict or execution authorization.
- `review` owns `Review Frame`, formal `Blocking Gaps`, gap severity, verdicts, whether a plan can be used for `Intended Next Use`, and change necessity judgment.
- For plan reviews, `Review Verdict: ready` means no blocking gaps for the intended next use. Do not block a plan for optional improvement only.
- `build` requires explicit user invocation and a plan concrete enough to execute safely; review is recommended for material risk, but missing review is not by itself a build blocker.

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
Task: <route|clarify|explore|distill|shape|plan|review|sync>
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

Use `Task: distill` instead of `explore` when the user asks to summarize, compress, or extract structure from specified source material. `distill` outputs `Observed`, `Inferred`, `Unknown`, and a candidate `Artifact: distillation`; use `review` for accuracy/source-of-truth judgments and `persist` to save the result.

### Persist Session Artifact

Use when the user wants to persist a session artifact from recent discussion, an existing draft, or user-provided source.

```text
Mode: persist
Task: persist
Artifact: <brief|note|shape|plan|review|distillation>
Brief Type: <general|external-goal; only for Artifact: brief>
Artifact State: <inbox|working|settled|superseded>
Thread: <thread-name>
Intent: <summary|exploration|decision|audit|handoff|constraint|reference|capture>
Depth: <compact|standard|detailed>
Topic: <topic>
Target: <optional when thread/topic can infer target>
Context:
- .workflow/tasks/persist.md
- .workflow/templates/<artifact template>.md and .workflow/templates/_persist_metadata.md
- selected lenses only when named
- Persist Candidate, explicit source, or source context
Request:
Persist the high-fidelity structured artifact only.
```

`persist` may infer `.session/inbox/**` and `.session/threads/{thread}/{artifact}_{topic}.md`. Thread directories are small closable work items. When `Thread` is absent, infer the target by same-work-item fit: reuse active threads for continuations/refinements/review responses, create or suggest a new thread for distinct bounded decisions or changes, and treat recency as supporting evidence only. Explicit `notes/**` targets are allowed only for disposable exploration notes and are never inferred. `.session/archive/**` targets route to `sync` with `Sync Domain: session-archive`. Targets outside active `.session/inbox/**`, `.session/threads/**`, and `notes/**` route to `sync`, `build`, or external-agent.

Resolve source in this order: explicit source/path, Artifact ID, Persist Candidate, same-work-item artifacts, then matching recent discussion and user corrections. Preserve decision-relevant reasoning, not full transcript. Keep context, key facts, decision trail, rejected options, risks, examples, and next use when they affect later work.

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

Build is a workflow-aware bounded executor. Before verification, establish `Execution Environment Contract` and command provenance: CWD, repo root, OS/shell, package manager or runner, available scripts, command source, and retry budget. Do not blindly retry path, cwd, shell, quoting, or command variants; default retry budget is 2 for the same failure class.

After build, use a concise `Execution Trace` for ordinary successful execution and automatically add expanded evidence for blocked, partial, failed verification, pitfall, reusable execution discovery, scope-expansion risk, or user-requested persistence. `Execution Trace` is factual, not a review verdict, and `build` must not write `.session/**`; persist current-work-item audit output as `Artifact: note`, `Intent: audit`, or reusable execution discoveries as `Artifact: note`, `Artifact State: inbox`, `Intent: capture`.

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
Lens: <none|consistency|language|architecture>
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
- .workflow/templates/project_doc.md, .workflow/templates/code_readme.md, or .workflow/templates/archive_summary.md plus .workflow/templates/_sync_metadata.md when creating or rewriting a stable target
- source artifact, existing docs, and relevant source files
Request:
Project confirmed outcomes into the target only. Use Sync Object gates for `project-docs`; use archive prerequisites for `session-archive`.
```

When creating a new `architecture`, `feature`, or `reference` docs target, add `.workflow/templates/project_doc.md`. When updating existing docs, preserve the target file's structure.

If the source is only `.session/inbox/**` or `notes/**`, require explicit source-of-truth confirmation before writing project docs.

For `session-archive`, require `Source Thread`, `Thread Status`, `Archive Purpose`, `Summary Scope`, `Next Retrieval Use`, and target `.session/archive/<thread>/summary.md`. Do not edit active `.session/threads/**`. A prior `distill archive-summary-draft` is optional; when archive prerequisites are complete, `sync` may generate the final archive summary inline.

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
Intent: <summary|exploration|decision|audit|handoff|constraint|reference|capture>
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

### Plan Review

```text
Use .workflow/codex.md as the Codex adapter.
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Review this Codex native plan before implementation with explicit critique posture. Use Review Type: verdict-review. Return ready, needs changes, needs more evidence, blocked, or docs blocked.
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
