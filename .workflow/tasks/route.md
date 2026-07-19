---
id: route
role: analyst
purpose: Recommend the smallest useful next path.
inputs:
  - user_goal
outputs:
  - chat_route
user_selectable_lenses: []
done_check:
  - next_task_is_named
  - write_path_is_clear
  - required_context_is_listed
---

# Route Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/analyst.md}}

## Mode Rules

- `route` is always chat-only.
- Start with `Interpreted goal`, then recommend the next task sequence, selected lenses, write path, and Add Context files.
- Do not load templates, create files, or apply lenses automatically.
- If no lens is named by the user, use `Lens: none`.

## When To Use

- Use when the user wants guidance, the request is mixed, the selected task seems wrong, or the next write path is unclear.
- Use when the safest output is a segmented path rather than immediate execution.

## Do Not Use When

- Do not use to perform analysis, planning, review, persist, sync, or implementation itself.
- Do not use to apply lenses; only recommend them.

## Boundary Layers

- `Core Responsibility`: classify intent and recommend the smallest useful task path, write path, lenses, context, and next prompt.
- `Adjacent Allowance`: include boundary mismatch, allowed scope, segmented prompts, handoff points, and lens suggestions when they help the user choose the next task.
- `Hard Authority Boundaries`: do not perform the routed task, apply lenses, run evidence preflight, write files, sync stable documents, execute, implement, or grant permissions to later segments.

Adjacent allowance must stay routing-owned. If the user needs actual analysis, verdict, planning, persistence, sync, or implementation, recommend that task and do not perform it inside `route`.

## Result Requirements

- Always chat-only.
- One standard chat response with interpreted goal, recommended path, selected task/lens, and next prompt.
- Add Context and handoff points only when composite work, boundary correction, or missing prerequisites make them useful.

## Task Boundary Check

`route` handles both active guidance and correction routing.

- Use `Scope Interpretation` when the user has selected a task but the request partially fits another output shape.
- Output `Recommended Segments` only for `composite`, `wrong_task`, or `missing_prerequisite`.
- Do not silently switch tasks or execute later segments.
- No implicit preflight runs in `route`. Do not scan evidence; only recommend which later task should run implicit preflight.

Boundary classes:

- `fits`: the selected task can handle the request.
- `fits_with_preflight`: the selected task can handle it after read-only preflight in `Mode: discuss`.
- `fallback_fit`: no task fits exactly, but the selected task can handle the primary user intent with only allowed adjacent output.
- `composite`: multiple tasks are needed.
- `wrong_task`: another task is the proper entrypoint and the selected task cannot provide a useful in-shape response.
- `missing_prerequisite`: target, explicit plan, source of truth, or Project Docs Rules safety is missing.

## Routing Model

Recommend the smallest path:

- When unsure, start with `shape`. Use `clarify` for semantic unpacking, `explore` for evidence acquisition and non-mutating probes, `distill` for user-directed summaries, and `review` for verdict or gap-analysis.
- User-selected task is respected; authority is not expanded. If no task fits exactly, choose the nearest task by primary user intent and output `Scope Interpretation`.
- Prefer `fits -> fits_with_preflight -> fallback_fit -> composite -> wrong_task`. Use `wrong_task` only when the selected task cannot provide a useful in-shape response.
- Nearest-fit fallback must output `Scope Interpretation`; it may perform only the chosen task's output shape plus allowed adjacent output.
- Ambiguous what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests default to `shape`.
- "I do not know how to proceed" goes to `shape`.
- "Roughly compress the current discussion, then give direction" goes to `shape` if the compression is lightweight and current-context only; use `distill -> shape` if a specified source summary matters.
- "Look at the risks, then give direction" goes to `shape` if this is a risk sketch; use `review` if the user needs a formal verdict, readiness judgment, or source-of-truth decision.
- "Give me rough steps" goes to `shape` if this means a conceptual planning sketch; use `plan` if sequencing, executable handoff, target files, or verification steps are needed.
- Meaning questions such as "what does this term/rule/sentence mean" or semantic difference questions not grounded in repo/source inspection go to `clarify`.
- Source-backed descriptive inquiry such as reading code, finding entrypoints, checking docs, understanding behavior, studying references, mapping observed differences, checking evidence, or running a non-mutating probe goes to `explore`.
- Existence and discovery questions such as "does this repo have evidence for X", "where is X", "how does X work", "what exists", "what is different between A and B in the repo", or "what evidence exists" go to `explore`.
- `explore` is descriptive inquiry for shape and review. Use `explore -> shape` when the user wants to design from observed evidence, and `explore -> review` when the user wants a verdict or gap analysis from evidence.
- Use `explore -> plan` only when the direction or target is already selected and exploration merely supplies repo-aware planning context.
- "review this system has X" can use `review` as a claim verdict with bounded evidence check.
- "review how X is implemented" should use an observed-system-map response or recommend `explore` unless the user asks for a verdict.
- "explore whether this plan is reasonable" should use `explore` evidence shape plus `Candidate Review Targets`, not a verdict.
- Summary and distillation requests such as "summarize this folder", "distill this thread", "extract structure", or "make an archive summary draft" go to `distill`.
- Gap and missing-capability requests such as "what is missing", "where are the gaps", "does this satisfy the goal", "audit feature gaps", or "audit workflow support" go to `review` with `Review Type: gap-analysis`; if evidence is insufficient, route `explore -> review`. "No evidence found for X" is explore; "X is a gap that matters" is review.
- Judgment questions such as "is this correct", "is this reasonable", "should this change", "which source is truth", or "is this ready" go to `review`.
- Change-seeking judgment questions such as "does this need change", "is this worth changing", "are there useful improvements", "should this be adjusted", or "audit improvement points" go to `review` with `Change Assessment`.
- "If changing, what directions exist" goes to `shape`.
- "How should the chosen change be implemented" or "give implementation steps" goes to `plan` when the direction is already selected.
- Verdict-only requests such as "is this reasonable", "can this execute", "does this conflict", or "is this ready" go to `review`.
- Default careful session flow: `external or conversational goal -> shape -> plan -> optional review -> build/external-agent -> recommended review -> sync`.
- Long or reusable external goal sources: `persist` as `Artifact: brief`, `Brief Type: external-goal`, then `shape` consumes the inbox brief.
- Current conversational goals: `shape` directly, then persist the shaped concept only when worth preserving.
- New background or staged requirements: `clarify` or `explore` -> `persist` to `.session/inbox/**`.
- User-directed summary or distillation: `distill` -> optional `persist Artifact=distillation`, `review`, `shape`, `plan`, or `sync`.
- Reusable execution discovery or build environment lesson: `build -> Persist Candidate -> persist` as `Artifact: note`, `Artifact State: inbox`, `Intent: capture`.
- Target direction, option, architecture, or concept: `shape` -> `persist` to `.session/threads/{thread}/shape_{topic}.md`.
- Evidence to direction: `explore -> shape`.
- Evidence to verdict or gap analysis: `explore -> review`.
- Evidence to plan: `explore -> plan` only after direction or target is selected.
- Repo-aware implementation sequence or handoff: `plan` -> `persist` to `.session/threads/{thread}/plan_{topic}.md`.
- Session artifact write: `persist` -> active `.session/inbox/**` or `.session/threads/**`.
- Active thread metadata or thread file maintenance: `persist` -> `.session/threads/**`.
- Completed thread archive summary: `review/plan -> sync` with `Sync Domain: session-archive` -> `.session/archive/<thread>/summary.md`.
- Native external-agent implementation: external-agent path -> `review` plan review -> native Implement -> `review` diff.
- Project docs alignment: `review -> plan -> sync` with `Sync Domain: project-docs` -> `docs/**` or `src/**/README.md`.
- Code or repository change through workflow: `build` with `Mode: execute`, explicit user invocation, and an explicit executable plan.
- Plan sufficiency loop: `plan insufficient -> shape/explore/user supplies input/plan`; `plan sufficient-for-draft -> review or persist`; `plan sufficient-for-handoff -> review, build with explicit invocation, or external-agent`; `plan + Review Verdict: ready -> build/external-agent`.
- Direct build path: `build` when the user invokes execute with an explicit executable plan; missing review is a risk notice, not a build blocker.
- Vague implementation intent without an explicit executable plan: `plan -> review`, then `build` or external-agent only after the plan is concrete enough.
- Recommend `review` before build for breaking changes, constraint overrides, public API, data, security, source-of-truth, stable docs projection, multi-surface plans, high reversal cost, or ambiguous verification.
- Discussion chains should end with `Persist Candidate` when the result is worth preserving. `persist` consumes the candidate, recent discussion, or an explicit source; the original discussion task does not write files.
- Do not recommend any lens as a skip or override mechanism. Lenses may strengthen analysis only; they do not change task responsibility, write permission, execute permission, or sync permission.

## Lens Suggestions

- Use `shape` built-in option comparison for route comparison or technical direction; do not suggest a separate strategy lens.
- Use core `Input Sufficiency` rules for planning, handoff-grade plans, strong-model-to-weak-model handoff, or requests to avoid premature code-level detail; do not suggest a separate conceptual lens.
- Suggest `boundary` for ownership, dependency direction, contract leakage, provider/package boundaries, provider-owned capability business, main-system business, or migration path back to a package.
- Suggest `architecture` for general structure, interfaces, public surfaces, constraints, or durable tradeoffs.
- For terminology or rules questions outside review, suggest `language`, `clarify`, or `shape` depending on the request; reserve `domain` for review.
- Use thread inference and `persist` rules for multi-turn work with changing background; do not suggest a separate iteration lens.
- For vague or early ideas, route to `shape` with embedded critique check rather than redteam.
- For an existing target where the user asks to find problems, critique, counterargue, identify pitfalls, or trace failure paths, recommend a review with explicit redteam critique posture.
- Do not enable `redteam` automatically; output it only as a critique recommendation unless the user explicitly selected it.
- Suggest `test` for verification planning.
- Suggest `ponytail` for an explicitly selectable demo-first PoC posture when the user wants the smallest credible happy path, controlled inputs, relaxed production validation, YAGNI, or protection from over-engineering. Do not enable it automatically.
- Suggest `debug` for defects or uncertain runtime behavior.
- Suggest `consistency` when the user asks for a source-of-truth or maintained-alignment judgment across explicit session sources, project docs, code, tests, code-adjacent README files, workflow artifacts, prompts, templates, or archive summaries.
- Suggest `boundary, consistency` when provider/contract/package boundary judgment also depends on source-of-truth, docs/code drift, contract/implementation alignment, or artifact alignment.
- Do not suggest `consistency` for discovery questions like whether evidence for a capability exists, where it is implemented, or how reliable the evidence is.
- Suggest `language` for terminology or output language.
- Recommend `distill` as a task when the user wants a summary, folder summary, source distillation, or archive-summary draft.
- Ask the selected task to expand within its standard response, or use persist `Depth: detailed` for a durable artifact; do not suggest a separate expand lens.

## Response Contract

Use one standard response. Keep the shared groups in order and omit optional
groups that have no content. Simple routes stay short; composite, wrong-task,
and missing-prerequisite routes add the support fields they need:

```text
User Intent
- Interpreted Goal: <one sentence>
Task State
- Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite; when useful>
- Boundary Advice: <Why, Useful Response Now, Advisory Next Task; when useful>
- Scope Interpretation: <Requested Task, Output Shape Used, Effective Scope, Out-of-Shape Material, Recommended Next Task; when useful>
Primary Result
- Recommended Path: <task -> task>
- Lens: <none or explicit lenses>
Supporting Information
- Mode: <discuss|persist|execute; when needed>
- Write Path: <workflow-managed|external-agent; when needed>
- Target: <only when writing>
- Add Context: <task, selected lenses, write templates only for persist/sync, and relevant sources; when needed>
- Recommended Segments: <composite segments with Continue Condition; only for composite requests>
- Advisory Handoff Points: <decision, audit, source-of-truth, docs, or diff-review point; only when relevant>
Next
- Next Prompt: <copyable prompt>
```

## User Input

{{goal, situation, or request for workflow guidance}}
