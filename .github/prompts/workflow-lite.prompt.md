---
description: Workflow Lite fallback/router prompt for mixed requests and full protocol control.
argument-hint: "Mode=<discuss|persist|execute>; Output=<compact|normal|full>; Write Path=<workflow-managed|external-agent>; Task=<route|clarify|explore|shape|plan|persist|build|review|sync>; Lens=<none|iteration|expand|consistency|distill|language|domain|strategy|conceptual|redteam|test|architecture|debug>; Intent=<summary|exploration|decision|audit|handoff|constraint|reference>; Depth=<compact|standard|detailed>; Thread=<thread-name>; Target=<required for sync/docs/code; optional for persist>; Plan=<required for execute>; Request=<what you want>"
---

# Workflow Lite Fallback / Router Prompt

Use this full-protocol prompt when the request is mixed, the right task is unclear, or you need explicit fields such as `Write Path`, `Mode`, `Target`, `Plan`, or `Scope`.

For common daily Copilot work, prefer dedicated workflow prompt commands:

- `/wf-route`
- `/wf-shape`
- `/wf-plan`
- `/wf-review`
- `/wf-persist`
- `/wf-sync`

## Required Input

```text
Mode: ${input:mode:discuss}
Output: ${input:output:compact|normal|full}
Write Path: ${input:write_path:workflow-managed|external-agent}
Task: ${input:task:route|clarify|explore|shape|plan|persist|build|review|sync}
Lens: ${input:lens:none; comma-separated lenses allowed only when explicitly selected}
Intent: ${input:intent:required for persist; otherwise none}
Depth: ${input:depth:compact|standard|detailed; optional for persist}
Target: ${input:target:required for sync/docs/code; optional for persist; otherwise none}
Thread: ${input:thread:optional for persist thread target inference}
Plan: ${input:plan:required for execute; otherwise none}
Request: ${input:request:describe the work}
```

## Rules

- Use exactly one task as the main workflow context.
- Prefer dedicated workflow prompt commands for common single-task work; use this prompt as fallback/router for mixed, unclear, or full-protocol requests.
- Default to `Mode: discuss`.
- Default to `Output: compact`.
- Start with an inline `Understanding Check` unless the request is trivial.
- Run a lightweight Task Boundary Check before acting.
- Classify boundary as `fits`, `fits_with_preflight`, `composite`, `wrong_task`, or `missing_prerequisite` when the request is not straightforward.
- If composite, output segmented prompts with stop points instead of forcing the request into one task.
- When unsure, start with `shape`.
- Ambiguous what-if, strategy, conceptual, direction-setting, or entrypoint-selection requests default to `shape`.
- Evidence-only requests go to `explore`; verdict-only requests go to `review`.
- Lenses may strengthen the selected task, but must not change task responsibility.
- Discussion freedom applies only in `Mode: discuss`: AI may provide `Provisional Recommendation`, `Candidate Options`, `Best Guess`, `Candidate Interpretations`, `Minimal Revision Sketch`, and `What Would Change My Mind` as thinking material.
- For uncertain or consequential discussion output, include `Confidence`, `Assumptions`, and `Human Decision Needed`.
- Compact output may include one best guess; do not hide useful provisional thinking behind only risks and blockers.
- `conceptual` is explicit-only. When selected, state `Planning Level: concept | high-level-plan | implementation-plan`; `build` does not require this label and still relies on explicit plan executability.
- Read-only preflight is allowed only in `Mode: discuss`; do not load templates, write files, run implementation, or apply unselected deep lenses during preflight.
- Implicit preflight defaults to `shape`, `plan`, and `sync`; conditional preflight applies to `review`, `build`, and `explore`; no implicit preflight runs for `clarify` or `route`.
- Ask for confirmation only when ambiguity would affect file writes, execution, source of truth, or material scope.
- Use `Lens: none` unless the user explicitly names or adds lens files.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed; follow the user's lens order.
- Do not infer, auto-apply, or load all lenses.
- In `Mode: discuss`, do not load templates and do not create or update files.
- Discussion tasks should produce a short `Persist Hint` when the result is worth preserving; output full `Persist Packet` only when `Output: full`, the user asks to persist, or a handoff/audit requires it.
- In `Mode: persist`, use `Task: persist` for `.session/**` artifacts or `Task: sync` for allowed project docs targets / `src/**/README.md`.
- For `persist`, `.session/inbox/**` and `.session/threads/{thread}/{artifact}_{topic}.md` targets may be inferred from `Artifact + Thread + Topic`.
- For `persist`, explicit `notes/**` targets may be written as disposable exploration memory; never infer `notes/**`.
- Use `persist shape_<topic>` to reference a shape by `Artifact ID`; in multi-topic discussion, persist the main goal over the latest topic unless explicitly targeted.
- `notes/**` is not project docs and is not an execution source.
- For `persist`, preserve decision-relevant reasoning, not full transcript. Discuss output budget does not reduce artifact depth.
- `persist` may apply explicit review edits, but must not choose a new direction, re-plan execution, or judge whether review feedback is correct.
- `Task: sync` in `Mode: persist` may write only allowed project docs targets or explicit `src/**/README.md` targets.
- In `Mode: execute`, require `Task: build` and an explicit executable plan.
- If using Codex/Copilot native Plan -> Implement, set `Write Path: external-agent`; external-agent is not a Mode.
- For `Write Path: external-agent`, audit the native plan before implementation and review the diff after implementation.
- Block instead of writing when `Mode: execute` lacks `Plan`, the target is outside the mode boundary, or instructions conflict.
- `docs/**` writes must follow Project Docs Rules.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`.
- `Compatibility: breaking` or `Constraint Mode: propose_override | prototype_exception` require explicit user or explicit-source intent.
- `shape` may suggest breaking/override pressure; `plan` must encode selected policy; `build` must stop on unplanned compatibility removal or constraint override.

## Project Docs Rules

- Source, scope, docs type, source of truth, and alignment success criteria must be clear.
- Allowed Project Doc Types: `docs/architecture/**`, `docs/design/**`, `docs/adr/**`, `docs/operations/**`, `docs/reference/**`, and `src/**/README.md`.
- No Workflow-Internal Docs Leakage: do not create `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares host-project taxonomy override.
- Preserve existing docs tone and structure when updating.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, temporary PoC detail, low-level implementation mirror content, and details that would mislead future execution.
- If source, scope, docs type, source of truth, alignment success criteria, or safety is unclear, output `docs blocked` and do not write `docs/**`.
- `docs/**` is updated only when drift would cause future code/docs alignment mistakes.

## Context Format

Add the selected task file from `.workflow/tasks/`.
Add the matching template from `.workflow/templates/` only for `persist` or `sync` in `Mode: persist`.
For new allowed `docs/**` targets, add `project_doc.md` or `architecture_note.md`; for existing docs, preserve the target structure.
Add selected lens files from `.workflow/lenses/` only when `Lens` is not `none`.
Add relevant `.session/goal/*`, `.session/inbox/**`, `.session/threads/**`, `docs/**`, and source files.

## Boundary Output

For `Output: compact`, prefer:

```text
Understanding: <one line>
Take:
- <3-5 bullets max>
Risks/Unknowns:
- <0-3 bullets>
Next:
- <one suggested next move>
Persist Hint: <none or one line>
```

For `Output: normal`, refine toward a future persist without writing files:

```text
Understanding: <one line>
Refined Direction / Plan:
- <key structure>
Important Context To Preserve:
- <phase, constraints, examples, accepted risks, user corrections>
Open Questions:
- <questions>
Persist Hint:
- <artifact/thread/topic/target>
```

Use `Recommended Segments` only for `composite`, `wrong_task`, or `missing_prerequisite`.

```text
Boundary: <fits|fits_with_preflight|composite|wrong_task|missing_prerequisite>
Reason: <one sentence>
Recommended Path: <task -> task>
Next Prompt: <copyable prompt>
```

For composite requests, provide:

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
- <where user decision, audit, or source-of-truth confirmation is required>
```

## External-Agent Review Formats

Plan audit:

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this external plan before native implementation. Return ready, needs changes, blocked, or docs blocked.
```

Diff review:

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the diff against the explicit external plan. Identify scope drift, missing verification, and follow-up.
```
