---
description: Workflow Lite fallback/router prompt for mixed requests and full protocol control.
argument-hint: "Mode=<discuss|persist|execute>; Output=<compact|normal|full>; Write Path=<workflow-managed|external-agent>; Task=<route|clarify|explore|distill|shape|plan|persist|build|review|sync>; Lens=<none|consistency|boundary|language|domain|redteam|test|architecture|debug|expert>; Intent=<summary|exploration|decision|audit|handoff|constraint|reference|capture>; Depth=<compact|standard|detailed>; Sync Domain=<project-docs|session-archive>; Thread=<thread-name>; Target=<required for sync stable documents; optional for persist>; Plan=<required for execute>; Request=<what you want>"
---

# Workflow Lite Fallback / Router Prompt

Use this full-protocol prompt when the request is mixed, the right task is unclear, or you need explicit fields such as `Write Path`, `Mode`, `Target`, `Plan`, or `Scope`.

For common daily Copilot work, prefer dedicated workflow prompt commands:

- `/wf-route`
- `/wf-distill`
- `/wf-shape`
- `/wf-plan`
- `/wf-pplan`
- `/wf-review`
- `/wf-persist`
- `/wf-sync`

## Required Input

```text
Mode: ${input:mode:discuss}
Output: ${input:output:compact|normal|full}
Write Path: ${input:write_path:workflow-managed|external-agent}
Task: ${input:task:route|clarify|explore|distill|shape|plan|persist|build|review|sync}
Lens: ${input:lens:none; comma-separated lenses allowed only when explicitly selected}
Intent: ${input:intent:required for persist; otherwise none}
Depth: ${input:depth:compact|standard|detailed; optional for persist}
Target: ${input:target:required for sync stable documents; optional for persist; otherwise none}
Sync Domain: ${input:sync_domain:project-docs|session-archive; required for sync when target does not make it obvious}
Thread: ${input:thread:optional for persist thread target inference}
Plan: ${input:plan:required for execute; otherwise none}
Request: ${input:request:describe the work}
```

## Rules

- Use exactly one task as the main workflow context.
- Prefer dedicated workflow prompt commands for common single-task work; use this prompt as fallback/router for mixed, unclear, or full-protocol requests.
- Default to `Mode: discuss`.
- Default to `Output: compact`.
- Start with `User Intent` unless the request is trivial; this must describe what the user wants, not the technical diagnosis.
- Run a lightweight Task Boundary Check before acting.
- Classify boundary as `fits`, `fits_with_preflight`, `fallback_fit`, `composite`, `wrong_task`, or `missing_prerequisite` when the request is not straightforward.
- User-selected task is respected; authority is not expanded.
- If no task fits exactly, choose the nearest task by primary user intent. Default gray-area discussion to `shape` only for concept direction, option framing, or next-step selection.
- For `fallback_fit`, output `Scope Interpretation`, `Adjacent Allowance Used`, and `Recommended Next Task`; do not cross the selected task's hard authority boundaries.
- Prefer `fits -> fits_with_preflight -> fallback_fit -> composite -> wrong_task`; use `wrong_task` only when the selected task cannot provide a useful in-shape response.
- If composite, output segmented prompts with stop points instead of forcing the request into one task.
- When unsure, start with `shape`.
- Meaning, explanation, restatement, difference, assumption, hidden scope, or prior AI answer unpacking requests go to `clarify`.
- Summary, folder summary, source distillation, and archive-summary draft requests go to `distill`.
- Ambiguous what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests default to `shape`.
- Evidence-only requests, discovery inventory, source-backed fact checks, and non-mutating probes go to `explore`; evidence-to-direction paths go `explore -> shape`; evidence-to-verdict/gap paths go `explore -> review`; evidence-to-plan paths use `explore -> plan` only when direction or target is already selected. Verdict, source-of-truth, missing-capability, and baseline-satisfaction requests go to `review`.
- Each task should answer using its own `Output Shape`: clarify=meaning, explore=evidence, shape=direction, review=verdict, plan=plan.
- Lenses may strengthen the selected task, but must not change task responsibility, write permission, execute permission, or sync permission. `distill` is a task, not a lens. Do not use any lens as a skip mechanism.
- Discussion freedom applies only in `Mode: discuss`: AI may provide lightweight next-task hints, `Provisional Recommendation`, `Candidate Options`, `Best Guess`, `Candidate Interpretations`, `Evidence Probes`, `Missing Evidence`, `Evidence Sufficiency`, `Downstream Use`, `Follow-up Targets`, `Minimal Revision Sketch`, `Repair Direction`, `Input Sufficiency`, `Input Gaps`, and `What Would Change My Mind` as thinking material.
- Discussion adjacency is allowed; authority is not. Adjacent output may recommend the next task, but write, sync, execute, implementation, source-of-truth, and build authority still require the proper `Mode`, `Task`, target rules, prerequisites, and explicit executable plan.
- For uncertain or consequential discussion output, include `Confidence`, `Assumptions`, and `Human Decision State`.
- Compact output may include one best guess; do not hide useful provisional thinking behind only risks and blockers.
- In `shape`, `Human Decision State` is control flow, not tail metadata. Put it after current read and before recommendation.
- If state is `checkpoint`, use `vscode/askQuestions` when available as the Copilot-only renderer for `User Checkpoint`.
- Use `User Checkpoint.Question` as the question, use 2-3 mutually exclusive `User Checkpoint.Options`, preserve label/explanation/risk, and put the recommended option first with `(Recommended)`.
- If `vscode/askQuestions` is unavailable, output structured `User Checkpoint` and wait. If state is `blocking`, stop before final recommendation and `Persist Candidate`.
- `vscode/askQuestions` is only a checkpoint renderer for `shape`.
- Do not use the native question UI for planning, review verdicts, task routing, preflight, write authorization, sync authorization, or build authorization.
- Use `Input Sufficiency: insufficient | sufficient-for-draft | sufficient-for-handoff` for planning output. This classifies source input for intended use, not generated plan quality.
- `plan compact` must summarize the chosen direction first, include `Motivation`, then give compact `Impact Surface`, `Plan At A Glance`, and plan body only when input is sufficient. Use `Shape Summary: Source=chat` when there is no persisted shape artifact; use `Motivation: unknown` rather than inventing. `Output: full` is a minimal handoff packet for persist, explicit handoff candidates, implementation handoff, or external-agent handoff; persisted artifact structure comes from `.workflow/templates/plan.md`.
- `review` owns `Review Verdict`, formal `Blocking Gaps`, severity, and gap analysis. Review output must include `Review Target Kind` and `Intended Next Use`; plan review is a built-in review rubric, not a lens. Use `Review Type: gap-analysis` for missing capability, unmet baseline, feature gap, workflow gap, or docs/code alignment gap.
- `explore` is upstream evidence for shape and review. It can say "evidence found" or "no evidence found"; `review` decides what that evidence means against a baseline. `explore` may run non-mutating probes only to establish evidence and must report `Probe`, `Command or Method`, `Observed Result`, `Reliability`, and `Side Effect Check`. Use `explore -> plan` only when direction or target is already selected and evidence only fills repo-aware planning context.
- Review plans under `verdict-review` when the question is whether the plan can be used for `Intended Next Use`. When a plan asks review to diagnose a system problem, use `verdict-review` or `gap-analysis` based on the question.
- For plan reviews, `Review Verdict: ready` means no blocking gaps for the intended next use. Do not block a plan for optional improvement only.
- Read-only preflight is allowed only in `Mode: discuss`; do not load templates, write files, run implementation, or apply unselected deep lenses during preflight. Shape/review/plan may perform bounded evidence checks only to support their own output shape; if evidence gathering becomes the main deliverable, route to `explore`.
- Plan preflight is mandatory bounded repo-fit preflight when planning depends on repo facts: target files or areas, existing patterns, constraints, verification entrypoints, and direction-to-repo fit for an already selected direction.
- Embedded critique is lightweight core behavior in `shape`, `plan`, and `build`; it names risks and stop conditions without loading the redteam lens or issuing review verdicts.
- Implicit preflight defaults to `shape`, `plan`, and `sync`; conditional preflight applies to `review`, `build`, and `explore`; no implicit preflight runs for `clarify` or `route`.
- Ask for confirmation only when ambiguity would affect file writes, execution, source of truth, or material scope.
- Use `Lens: none` unless the user explicitly names or adds lens files.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed; follow the user's lens order.
- Use `boundary` for ownership, dependency direction, contract leakage, provider/package boundaries, provider-owned capability business, main-system business, and migration ownership. Use `boundary, consistency` together when boundary judgment also depends on source-of-truth, docs/code drift, contract/implementation alignment, or artifact alignment.
- Do not infer, auto-apply, or load all lenses.
- In `Mode: discuss`, do not load templates and do not create or update files.
- Discussion tasks should produce a short `Persist Candidate` when the result is worth preserving; this is only a candidate and must not write files. Output full `Persist Packet` only when `Output: full`, the user asks to persist, or a handoff/audit requires it.
- In `Mode: persist`, use `Task: persist` for active `.session/inbox/**` or `.session/threads/**` artifacts, and use `Task: sync` for stable-document targets: allowed project docs targets, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.
- For `persist`, `.session/inbox/**` targets may be inferred from `Artifact State: inbox`; `.session/threads/{thread}/{artifact}_{topic}.md` targets may be inferred from explicit `Thread + Artifact + Topic` or automatic same-work-item fit.
- Use `Intent: capture` for untriaged inbox knowledge captures such as reusable build execution discoveries. Inbox capture is not source of truth and is not an execution source; promote stable conclusions later through `review`, `plan`, or `sync`.
- External goals have two valid paths: long or reusable sources may be persisted as `Artifact: brief` with `Brief Type: external-goal` before `shape`; conversational goals may go directly to `shape`.
- `shape` is the reasoned projection from current chat or an inbox goal brief; the durable shaped result is a `.session/threads/**/shape_*.md` artifact.
- For `persist`, explicit `notes/**` targets may be written as disposable exploration memory; never infer `notes/**`.
- Use `persist shape_<topic>` to reference a shape by `Artifact ID`; this anchors source context and does not derive the thread directory. Infer thread targets by same-work-item fit and include `Thread Inference Note` when assumptions matter.
- `notes/**` is not project docs and is not an execution source.
- For `persist`, preserve decision-relevant reasoning, not full transcript. Discuss output budget does not reduce artifact depth.
- `persist` may apply explicit review edits, but must not choose a new direction, re-plan execution, or judge whether review feedback is correct.
- `Task: sync` in `Mode: persist` may write only stable-document targets for its selected `Sync Domain: project-docs | session-archive`.
- In `Mode: execute`, require `Task: build` and an explicit executable plan.
- If using Codex/Copilot native Plan -> Implement, set `Write Path: external-agent`; external-agent is not a Mode.
- For `Write Path: external-agent`, recommend native plan review before implementation and diff review afterward when risk is material.
- Block instead of writing when `Mode: execute` lacks `Plan`, the target is outside the mode boundary, or instructions conflict.
- For `Task: build`, establish `Execution Environment Contract` before verification: CWD, repo root, OS/shell, package manager or runner, available scripts, command source, and retry budget.
- Build verification commands require `Command Provenance`: plan, repo script, Makefile, project docs, CI, or confirmed repo fact. Do not blindly retry path/cwd/shell/quoting variants; default retry budget is 2 for the same failure class.
- Build outputs `Execution Trace`, not review verdicts. It may suggest `Persist Candidate: Artifact=note; Intent=audit` for current work-item audit, or `Artifact=note; Artifact State=inbox; Intent=capture` for reusable execution discoveries.
- `docs/**` writes must follow Project Docs Rules.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`.
- `Compatibility: breaking` or `Constraint Mode: propose_override | prototype_exception` require explicit user or explicit-source intent.
- `shape` may suggest breaking/override pressure; `plan` must encode selected policy; `build` must stop on unplanned compatibility removal or constraint override.

## Project Docs Rules

- Source, scope, sync object, source of truth, alignment success criteria, and safe target selection must be clear.
- Sync Objects: `architecture | feature | reference | code-readme | archive-summary | all`.
- No Workflow-Internal Docs Leakage: do not create `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares host-project taxonomy override.
- Preserve existing docs tone and structure when updating.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, temporary PoC detail, low-level implementation mirror content, and details that would mislead future execution.
- If source, scope, sync object, source of truth, alignment success criteria, target selection, or safety is unclear, output `docs blocked` and do not write `docs/**`.
- `docs/**` is updated only when drift would cause future code/docs alignment mistakes.

## Archive Rules

- `Sync Domain: session-archive` writes only `.session/archive/<thread>/summary.md`.
- Required: Source Thread, Thread Status, Archive Purpose, Summary Scope, Next Retrieval Use, and target.
- Do not write `.session/threads/**` or `.session/inbox/**` through sync; active session memory belongs to `persist`.
- Output `archive blocked` when archive prerequisites are unclear.

## Context Format

Add the selected task file from `.workflow/tasks/`.
Add the matching template from `.workflow/templates/` only for `persist` or `sync` in `Mode: persist`, plus `_persist_metadata.md` for `persist` or `_sync_metadata.md` for `sync`.
Discussion task `Full Persist Packet` output is handoff input, not a final artifact schema.
For new `architecture | feature | reference` docs targets, add `project_doc.md`; for `code-readme`, add `code_readme.md`; for archive summaries, add `archive_summary.md`. For existing docs, preserve the target structure.
Add selected lens files from `.workflow/lenses/` only when `Lens` is not `none`.
Add relevant `.session/inbox/**`, `.session/threads/**`, `docs/**`, and source files.

## Boundary Output

For `Output: compact`, prefer:

```text
User Intent: <one line about what the user wants>
Current Read: <optional one line about relevant code/docs/discussion facts>
Take:
- <3-5 bullets max>
Risks/Unknowns:
- <0-3 bullets>
Next:
- <one suggested next move>
Persist Candidate: <none or one line; candidate only, do not write>
```

For `Task: plan` with `Output: compact`, use this structure instead:

```text
User Intent: <one line about what the user wants planned>
Input Sufficiency: <insufficient|sufficient-for-draft|sufficient-for-handoff>
Input Gaps:
- <only when insufficient; missing input categories only>
Shape Summary:
- Source: <chat | shape artifact | inbox brief | decision | project docs>
- Motivation: <one sentence or unknown>
- Selected Direction: <one line>
- Key Decisions: <1-3 bullets or none>
- Assumptions: <0-2 bullets or none>
Impact Surface:
- Scope Size: <small | medium | large; omit when insufficient>
- Affected Surfaces: <surfaces; omit when insufficient>
- Risk: <low | medium | high; omit when insufficient>
- Reversal Cost: <low | medium | high; omit when insufficient>
Plan At A Glance:
- <1-3 summary changes; target, reason, and risk; omit when insufficient>
Plan:
- <3-6 work packages or phases; omit when insufficient>
Compatibility / Constraint Plan:
- <when relevant>
Recommended Next Task: <shape | explore | review | plan | persist | sync | build | external-agent | none>
Next: <review plan | build with explicit invocation | persist plan | sync | shape | none>
Persist Candidate: <none or one line; candidate only, do not write>
```

For `Output: normal`, refine toward a future persist without writing files:

```text
User Intent: <one line about what the user wants>
Current Read: <optional one line about relevant code/docs/discussion facts>
Refined Direction / Plan:
- <key structure>
Discussion Notes To Preserve:
- <phase, constraints, examples, accepted risks, user corrections>
Questions:
- <questions>
Persist Candidate:
- <artifact/thread/topic/target>
```

For `Task: plan` with `Output: normal` or `Output: full`, follow `.workflow/tasks/plan.md`: include `Input Sufficiency`, conditional `Input Gaps`, `Shape Summary` with `Motivation`, `Impact Surface`, `Plan At A Glance`, `Plan` when input is sufficient, and `Compatibility / Constraint Plan` when relevant. Do not output formal blocking gaps, severity, review verdicts, or review-style checklists from `plan`.

Use `Recommended Segments` only for `composite`, `wrong_task`, or `missing_prerequisite`.

```text
Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite>
Scope Interpretation:
- Requested Task: <task named or implied by user>
- Output Shape Used: <meaning|evidence|direction|verdict|plan|persist|sync|build>
- Effective Scope: <what this task can answer now>
- Out-of-Shape Material: <what belongs to another task, or none>
- Recommended Next Task: <task or none>
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

Plan review:

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Review Target Kind: plan
Intended Next Use: external-agent
Review this external plan before native implementation with explicit critique posture. Return Review Verdict, Blocking Gaps, Non-blocking Gaps, Can Use For Intended Next Use, and Recommended Next Task.
```

Diff review:

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the diff against the explicit external plan. Identify scope drift, missing verification, and follow-up.
```
