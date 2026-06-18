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
- `Adjacent Allowance`: include boundary mismatch, allowed scope, segmented prompts, stop points, and lens suggestions when they help the user choose the next task.
- `Forbidden Authority`: do not perform the routed task, apply lenses, run evidence preflight, write files, sync stable documents, execute, implement, or grant permissions to later segments.

Adjacent allowance must stay routing-owned. If the user needs actual analysis, verdict, planning, persistence, sync, or implementation, route to that task and stop.

## Expected Output

- Always chat-only.
- `Output: compact` default: interpreted goal, recommended path, selected task/lens, and next prompt.
- Full Add Context and stop points only for composite work or `Output: full`.

## Task Boundary Check

`route` handles both active guidance and correction routing.

- Use `Boundary Assessment` when the user has selected a task but the request appears composite, wrong-task, or missing prerequisites.
- Output `Recommended Segments` only for `composite`, `wrong_task`, or `missing_prerequisite`.
- Do not silently switch tasks or execute later segments.
- No implicit preflight runs in `route`. Do not scan evidence; only recommend which later task should run implicit preflight.

Boundary classes:

- `fits`: the selected task can handle the request.
- `fits_with_preflight`: the selected task can handle it after read-only preflight in `Mode: discuss`.
- `fallback_fit`: no task fits exactly, but the selected task can handle the primary user intent with only allowed adjacent output.
- `composite`: multiple tasks are needed.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: target, explicit plan, source of truth, or Project Docs Rules safety is missing.

## Routing Model

Recommend the smallest path:

- When unsure, start with `shape`. Use `explore` for evidence, `distill` for user-directed summaries, and `review` for verdict.
- If no task fits exactly, choose the nearest task by primary user intent. Default gray-area discussion to `shape` only when the primary intent is concept direction, option framing, or next-step selection.
- Nearest-fit fallback must output `Boundary Mismatch` and `Allowed Scope`; it may perform only the chosen task's core responsibility plus allowed adjacent output.
- Ambiguous what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests default to `shape`.
- "I do not know how to proceed" goes to `shape`.
- "Roughly compress the current discussion, then give direction" goes to `shape` if the compression is lightweight and current-context only; use `distill -> shape` if a specified source summary matters.
- "Look at the risks, then give direction" goes to `shape` if this is a risk sketch; use `review` if the user needs a formal verdict, readiness judgment, or source-of-truth decision.
- "Give me rough steps" goes to `shape` if this means a conceptual planning sketch; use `plan` if sequencing, executable handoff, target files, or verification steps are needed.
- Evidence-only requests such as reading code, finding entrypoints, checking docs, understanding behavior, or studying references go to `explore`.
- Existence and discovery questions such as "does this repo have X", "where is X", "how does X work", or "what evidence exists" go to `explore`.
- Summary and distillation requests such as "summarize this folder", "distill this thread", "extract structure", or "make an archive summary draft" go to `distill`.
- Gap and missing-capability requests such as "what is missing", "where are the gaps", "does this satisfy the goal", "audit feature gaps", or "audit workflow support" go to `review` with `Review Type: gap-analysis`; if evidence is insufficient, route `explore -> review`.
- Judgment questions such as "is this correct", "is this reasonable", "should this change", "which source is truth", or "is this ready" go to `review`.
- Verdict-only requests such as "is this reasonable", "can this execute", "does this conflict", or "is this ready" go to `review`.
- Default careful session flow: `external or conversational goal -> shape -> plan -> optional review -> build/external-agent -> recommended review -> sync`.
- Long or reusable external goal sources: `persist` as `Artifact: brief`, `Brief Type: external-goal`, then `shape` consumes the inbox brief.
- Current conversational goals: `shape` directly, then persist the shaped concept only when worth preserving.
- New background or staged requirements: `clarify` or `explore` -> `persist` to `.session/inbox/**`.
- User-directed summary or distillation: `distill` -> optional `persist Artifact=distillation`, `review`, `shape`, `plan`, or `sync`.
- Reusable execution discovery or build environment lesson: `build -> Persist Candidate -> persist` as `Artifact: note`, `Artifact State: inbox`, `Intent: capture`.
- Target direction, option, architecture, or concept: `shape` -> `persist` to `.session/threads/{thread}/shape_{topic}.md`.
- Repo-aware implementation sequence or handoff: `plan` -> `persist` to `.session/threads/{thread}/plan_{topic}.md`.
- Session artifact write: `persist` -> active `.session/inbox/**` or `.session/threads/**`.
- Active thread metadata or thread file maintenance: `persist` -> `.session/threads/**`.
- Completed thread archive summary: `review/plan -> sync` with `Sync Domain: session-archive` -> `.session/archive/<thread>/summary.md`.
- Native external-agent implementation: external-agent path -> `review` plan review -> native Implement -> `review` diff.
- Project docs alignment: `review -> plan -> sync` with `Sync Domain: project-docs` -> `docs/**` or `src/**/README.md`.
- Code or repository change through workflow: `build` with `Mode: execute`, explicit user invocation, and an explicit executable plan.
- Plan readiness loop: `plan incomplete -> shape/explore/user-answer/plan`; `plan reviewable -> review or persist`; `plan execution-candidate -> optional review or build with explicit invocation`; `plan execution-candidate + Review Verdict: ready -> build/external-agent`.
- Direct build path: `build` when the user invokes execute with an explicit executable plan; missing review is a risk notice, not a build blocker.
- Vague implementation intent without an explicit executable plan: `plan -> review`, then `build` or external-agent only after the plan is concrete enough.
- Recommend `review` before build for breaking changes, constraint overrides, public API, data, security, source-of-truth, stable docs projection, multi-surface plans, high reversal cost, or ambiguous verification.
- Discussion chains should end with `Persist Candidate` when the result is worth preserving. `persist` consumes the candidate, recent discussion, or full packet; the original discussion task does not write files.
- Do not recommend any lens as a skip or override mechanism. Lenses may strengthen analysis only; they do not change task responsibility, write permission, execute permission, or sync permission.

## Lens Suggestions

- Use `shape` built-in option comparison for route comparison or technical direction; do not suggest a separate strategy lens.
- Use core `Plan Readiness` rules for planning, executable plan candidates, strong-model-to-weak-model handoff, or requests to avoid premature code-level detail; do not suggest a separate conceptual lens.
- Suggest `architecture` for boundaries, dependency direction, public surfaces, or constraints.
- For terminology or rules questions outside review, suggest `language`, `clarify`, or `shape` depending on the request; reserve `domain` for review.
- Use thread inference and `persist` rules for multi-turn work with changing background; do not suggest a separate iteration lens.
- For vague or early ideas, route to `shape` with embedded critique check rather than redteam.
- For an existing target where the user asks to find problems, critique, counterargue, identify pitfalls, or trace failure paths, recommend a review with explicit redteam critique posture.
- Do not enable `redteam` automatically; output it only as a critique recommendation unless the user explicitly selected it.
- Suggest `test` for verification planning.
- Suggest `debug` for defects or uncertain runtime behavior.
- Suggest `consistency` when the user asks for a source-of-truth or maintained-alignment judgment across explicit session sources, project docs, code, tests, code-adjacent README files, workflow artifacts, prompts, templates, or archive summaries.
- Do not suggest `consistency` for discovery questions like whether a capability exists, where it is implemented, or how reliable the evidence is.
- Suggest `language` for terminology or output language.
- Recommend `distill` as a task when the user wants a summary, folder summary, source distillation, or archive-summary draft.
- Use `Output: normal|full` or persist `Depth: detailed` when a compact decision or plan needs examples, pseudocode, or split parts; do not suggest a separate expand lens.

## Output Format

For normal requests, keep the route compact:

```text
Interpreted goal: <one sentence>
Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite, when useful>
Boundary Mismatch: <none or why no task fits exactly, when fallback_fit>
Allowed Scope: <core responsibility plus any allowed adjacent output, when fallback_fit>
Recommended path: <task -> task>
Lens: <none or explicit lenses>
Next prompt: <copyable prompt>
```

Use the full format only for `Output: full`, composite routing, wrong-task correction, or missing prerequisites:

```text
Interpreted goal: <one sentence>
Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite>
Boundary Mismatch: <none or why no task fits exactly>
Allowed Scope: <core responsibility plus any allowed adjacent output>
Recommended path: <task -> task>
Mode: <discuss|persist|execute>
Write Path: <workflow-managed|external-agent>
Lens: <none or explicit lenses>
Target: <only when writing>
Add Context:
- .workflow/tasks/<task>.md
- .workflow/lenses/<lens>.md only when selected
- .workflow/templates/<template>.md only for `persist` or `sync` when persisting
- relevant .session/inbox/**, relevant .session/threads/**, docs/**, or source files as needed
Next prompt: <copyable prompt>
```

For composite requests:

```text
Recommended Segments:
1. <segment name>
   Mode:
   Task:
   Lens:
   Target/Plan:
   Context:
   Request:
   Expected Output:
   Continue Condition:
Stop Points:
- <user decision, audit, source-of-truth, Project Docs Rules, or diff review point>
```

## User Input

{{goal, situation, or request for workflow guidance}}
