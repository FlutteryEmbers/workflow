---
description: Workflow Lite fallback/router prompt for mixed requests and full protocol control.
argument-hint: "Mode=<discuss|persist|execute>; Output=<compact|normal|full>; Write Path=<workflow-managed|external-agent>; Task=<route|clarify|explore|distill|shape|plan|persist|build|review|sync>; Lens=<none|consistency|boundary|language|domain|redteam|test|architecture|debug|expert|ponytail>; Intent=<summary|exploration|decision|audit|handoff|constraint|reference|capture>; Depth=<compact|standard|detailed>; Sync Domain=<project-docs|session-archive>; Thread=<thread-name>; Target=<required for sync stable documents; optional for persist>; Plan=<required for execute>; Request=<what you want>"
---

# Workflow Lite Fallback / Router Prompt

Use this full-protocol prompt when the request is mixed, the right task is unclear, or you need explicit fields such as `Write Path`, `Mode`, `Target`, `Plan`, or `Scope`.

For common daily Copilot work, prefer dedicated workflow prompt commands:

- `/wf-route`
- `/wf-clarify`
- `/wf-explore`
- `/wf-distill`
- `/wf-shape`
- `/wf-plan`
- `/wf-pplan`
- `/wf-review`
- `/wf-persist`
- `/wf-build`
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
- If composite, output segmented prompts with handoff points instead of forcing the request into one task.
- When unsure, start with `shape`.
- Meaning, explanation, restatement, semantic difference, assumption, hidden scope, or prior AI answer unpacking requests go to `clarify`.
- Summary, folder summary, source distillation, and archive-summary draft requests go to `distill`.
- Ambiguous what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests default to `shape`.
- Source-backed how-it-works, what-exists, where-is, observed-difference, evidence-check, entrypoint-flow, discovery inventory, source-backed fact check, and non-mutating probe requests go to `explore`; evidence-to-direction paths go `explore -> shape`; evidence-to-verdict/gap paths go `explore -> review`; evidence-to-plan paths use `explore -> plan` only when direction or target is already selected. Verdict, source-of-truth, missing-capability, baseline-satisfaction, and worth-changing requests go to `review`.
- Change-seeking judgment routes: "does this need change / is it worth changing / any useful improvements" goes to `review` with `Change Assessment`; "if changing, what directions exist" goes to `shape`; "how to make the chosen change" goes to `plan`.
- Each task should answer using its own `Output Shape`: clarify=meaning, explore=observed system map, shape=direction, review=verdict, plan=plan.
- Lenses may strengthen the selected task, but must not change task responsibility, write permission, execute permission, or sync permission. `distill` is a task, not a lens. Do not use any lens as a skip mechanism.
- Discussion Advisory Principle applies only in `Mode: discuss`: AI may provide lightweight next-task hints, `Need For Shape`, `Shape Continuation`, `Boundary Advice`, `Provisional Recommendation`, `Candidate Options`, `Best Guess`, `Candidate Interpretations`, `Evidence Probes`, `Missing Evidence`, `Evidence Sufficiency`, `Downstream Use`, `Follow-up Targets`, `Minimal Revision Sketch`, `Repair Direction`, `Input Sufficiency`, `Input Gaps`, `Planning Continuation`, and `What Would Change This` as thinking material.
- Discussion adjacency is allowed; authority is not. Adjacent output may recommend the next task, but write, sync, execute, implementation, source-of-truth, and build authority still require the proper `Mode`, `Task`, target rules, prerequisites, and explicit executable plan.
- For uncertain or consequential discussion output, include `Confidence`, `Assumptions`, and `Human Decision State`.
- Compact output may include one best guess; do not hide useful provisional thinking behind only risks and blockers.
- In `shape`, `Need For Shape` is an advisory continuation classifier. Put it after current read and before `Shape Continuation`.
- In `shape`, `Human Decision State` is discussion guidance, not authorization. Put it after `Shape Continuation` and before finalized recommendation when a user-owned choice matters.
- If `Need For Shape Status: needs-direction` and state is `checkpoint`, use `vscode/askQuestions` when available as the Copilot-only renderer for `User Checkpoint`.
- Use `User Checkpoint.Question` as the question, use 2-3 mutually exclusive `User Checkpoint.Options`, preserve label/explanation/risk, and put the recommended option first with `(Recommended)`.
- If that checkpoint UI is unavailable, output structured `User Checkpoint` and wait. If state is `unresolved`, continue with provisional direction, assumptions, what would change it, and advisory next task.
- `vscode/askQuestions` renders exactly two checkpoints: `shape` `User Checkpoint`, and `plan` `Compatibility Intake` after repo preflight satisfies every trigger in `.workflow/tasks/plan.md`.
- For a triggered `Compatibility Intake`, ask consumer scope and data/config lifecycle in one UI round; add cutover style only for external consumers, persisted data, or material transition cost. Give each question 2-3 mutually exclusive options, with the recommended option first and marked `(Recommended)`. When the third question is omitted, use the preflight-established atomic cutover for mapping. `ponytail` may favor demo-only, rebuild, and atomic cutover for controlled/disposable evidence but must not auto-select breaking.
- Continue the complete Plan after native answers. If native UI is unavailable, output `Input Sufficiency: insufficient`, `Input Gaps: compatibility policy`, and the structured intake, then wait without a plan or handoff body.
- Do not use native questions for fact discovery, ordinary clarification, review verdicts, technical solution delegation, target selection, task routing, repo preflight, write authorization, sync authorization, or build authorization. Plan questions outside `Compatibility Intake` remain forbidden.
- Use `Input Sufficiency: insufficient | sufficient-for-draft | sufficient-for-handoff` for planning output. This classifies source input for intended use, not generated plan quality or authorization.
- `plan compact` must summarize the chosen direction first, include `Motivation`, and when input is insufficient output `Planning Continuation` instead of an executable or handoff plan body. When input is sufficient, give compact `Impact Surface`, `Plan At A Glance`, plan body, and compact verification. Use `Shape Summary: Source=chat` when there is no persisted shape artifact; use `Motivation: unknown` rather than inventing. `Output: full` is a minimal handoff packet for persist, explicit handoff candidates, implementation handoff, or external-agent handoff; persisted artifact structure comes from `.workflow/templates/plan.md`.
- Default plan verification is minimum viable verification: prefer existing fixture/unit/static/smoke/targeted checks, repo scripts, prompt/static assertions, or manual acceptance checks over ideal high-assurance test systems. Old baseline, contract freeze, parity matrix, full regression, and e2e belong to `Lens: test` or explicit higher-assurance requests, not default plan prerequisites.
- `review` owns `Review Verdict`, formal `Blocking Gaps`, severity, gap analysis, and change necessity judgment. Review output must start with `Review Frame` containing `Review Question`, `Review Target Kind`, `Intended Next Use`, `Review Type`, and `Review Route Reason`; plan review is a built-in review rubric, not a lens. Use `Review Type: gap-analysis` for missing capability, unmet baseline, feature gap, workflow gap, or docs/code alignment gap. Use `Change Assessment` only for change-seeking review requests.
- `explore` is descriptive inquiry for shape and review. It can say how something works, what exists, where it appears, what source-backed differences were observed, and "no evidence found in checked scope"; `review` decides what that evidence means against a baseline. `explore` may run non-mutating probes only to establish evidence and must report `Probe`, `Command or Method`, `Observed Result`, `Reliability`, and `Side Effect Check`. Use `explore -> plan` only when direction or target is already selected and evidence only fills repo-aware planning context.
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
- Use `ponytail` only when explicitly selected for `shape`, `plan`, or `review`. It applies a demo-first PoC posture with controlled inputs, relaxed production validation, no speculative fields or abstractions, explicit deferred work, upgrade triggers, retained real-world safety, and minimum proof. Do not activate it from PoC/demo wording, persist it across requests, infer compatibility/constraint policy from it, or pass it to `build`.
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

For `Task: explore` with `Output: compact`, use this structure instead:

```text
User Intent: <one line about what the user wants to understand>
Explore Frame:
- Explore Question: <what the user wants to understand>
- Inquiry Type: how-it-works | what-exists | difference-map | evidence-check | entrypoint-map
- Source Scope: <checked files/docs/commands/materials>
- Downstream Use: shape | review | plan | none
- Stop Rule: <when enough descriptive evidence has been collected>
Observed Answer:
- How It Works: <only when relevant; observed behavior or mechanism>
- What Exists: <only when relevant; source-backed inventory>
- Differences Observed: <only when relevant; observed differences without judging which side is correct>
- Entrypoints / Flow: <only when relevant; observed entrypoints or flow>
- Not Found In Checked Scope: <evidence not found, with checked scope>
- Not Checked: <scope not checked>
Evidence Basis:
- <source -> observed fact>
Reliability / Not Checked:
- <reliability notes, weak evidence, or unchecked scope notes>
Evidence Probes:
- <only when used; probe, command or method, observed result, reliability, side effect check>
Evidence Sufficiency:
- For Shape: <sufficient|partial|insufficient>
- For Review: <sufficient|partial|insufficient>
- For Plan: <sufficient|partial|insufficient|not-applicable>
Downstream Use:
- Shape-ready Evidence: <evidence or none>
- Review-ready Evidence: <evidence or none>
- Plan-ready Evidence: <only when direction/target is selected; otherwise not-applicable>
Candidate Review Targets:
- <reviewable question or none>
Recommended Next Task: <shape|review|persist|distill|none; plan only when direction/target is already selected>
Persist Candidate: none | Artifact=note; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

For `Task: shape` with `Output: compact`, use this structure instead:

```text
User Intent: <one line about what the user wants shaped>
Current Read: <optional one line about relevant code/docs/discussion facts>
Need For Shape:
- Status: needs-direction | already-settled | answerable-now | needs-evidence | needs-review
- Reason: <why this continuation type fits>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
Shape Continuation:
- Continuation Type: <new-direction|carry-forward|short-answer|provisional|review-handoff>
- Current / Provisional Direction: <direction, short answer, or none>
- Carry Forward: <boundaries, assumptions, or next-use notes>
- What Would Change This: <evidence, review result, or user decision>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
Boundary Advice:
- Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite>
- Why: <routing reason or none>
- Useful Response Now: <what shape can still safely provide, or none>
- Advisory Next Task: <task or sequence>
Adjacent Allowance Used: <none|clarification|compression|evidence-needs|risk-sketch|planning-sketch>
Decision State:
- Human Decision State: <none|assumed|checkpoint|unresolved>
- Decision State Reason: <why this state applies>
- Assumed Default: <recommended default or none>
- Checkpoint Needed: <yes/no>
User Checkpoint: <only when Human Decision State is checkpoint; wait for selection before final recommendation>
Unresolved Decision: <only when Human Decision State is unresolved; evidence, decision, or review result needed>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Provisional Recommendation: <best guess, carry-forward recommendation, or none>
Impact Surface:
- <when this response forms or updates a direction and planning may follow>
Recommended Next Task: <clarify|explore|distill|review|plan|persist|sync|build|external-agent|none>
Persist Candidate: <none unless this response forms or updates a direction; otherwise Artifact=shape; Artifact ID=shape_<topic>; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/shape_<topic>.md>
```

For `Task: plan` with `Output: compact`, use this structure instead:

```text
User Intent: <one line about what the user wants planned>
Input Sufficiency: <insufficient|sufficient-for-draft|sufficient-for-handoff>
Input Gaps:
- <only when insufficient; missing input categories only>
Planning Continuation:
- Known Direction: <known chosen direction, or unknown; only when insufficient>
- Useful Planning Frame Now: <safe partial framing, or none; only when insufficient>
- Cannot Produce Yet: <draft plan | handoff plan | executable plan; only when insufficient>
- Advisory Next Task: <shape|explore|plan|none; only when insufficient>
Compatibility Intake:
- <only when triggered and native UI is unavailable; include evidence and 2-3 questions, omit all plan-body fields, and wait>
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
Verification:
- Minimum Viable Verification: <targeted fixture/unit/static/smoke/manual check; omit when insufficient>
- Verification Feasibility: <available|partial|unavailable|unknown; omit when insufficient>
- Fallback Verification: <fallback or none; omit when insufficient>
- Residual Risk: <remaining risk or none; omit when insufficient>
Compatibility / Constraint Plan:
- <when relevant>
Execution Handoff: <use Output: full or persisted plan for executable handoff; include only when Recommended Next Task is build or external-agent>
Recommended Next Task: <shape | explore | review | plan | persist | sync | build | external-agent | none>
Next: <shape | explore | user-answer | plan | review plan | build with explicit invocation | persist plan | sync | none>
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

For `Task: plan` with `Output: normal` or `Output: full`, follow `.workflow/tasks/plan.md`: include `Input Sufficiency`, conditional `Input Gaps`, `Planning Continuation` when insufficient, conditional `Compatibility Intake`, `Shape Summary` with `Motivation`, `Impact Surface`, `Plan At A Glance`, `Plan` when input is sufficient, `Verification` with minimum viable verification, feasibility, fallback, and residual risk, and `Compatibility / Constraint Plan` when relevant. Do not output a plan body while Compatibility Intake is unresolved. Do not output formal blocking gaps, severity, review verdicts, or review-style checklists from `plan`.

For non-trivial `Task: review` output, follow `.workflow/tasks/review.md` instead of the generic compact/normal structure. Include `Review Frame`, `Review Verdict`, `Confidence`, `Readiness`, `Blocking Gaps`, `Non-blocking Gaps`, `Can Use For Intended Next Use`, `Recommended Action`, `Suggested Critique`, and `Recommended Next Task`.

Use `Recommended Segments` only for `composite`, `wrong_task`, or `missing_prerequisite`.

```text
Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite>
Boundary Advice:
- Why: <routing reason or none>
- Useful Response Now: <what the selected task can still safely provide, or none>
- Advisory Next Task: <task or sequence>
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
Advisory Handoff Points:
- <where user decision, audit, or source-of-truth confirmation may be needed>
```

## External-Agent Review Formats

Plan review:

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Review Frame:
- Review Question: Can this external plan be used for native implementation?
- Review Target Kind: plan
- Intended Next Use: external-agent
- Review Type: verdict-review
- Review Route Reason: plan usability for external-agent implementation requires review, not shape or plan.
Review this external plan before native implementation with explicit critique posture. Return Review Verdict, Confidence, Readiness, Blocking Gaps, Non-blocking Gaps, Can Use For Intended Next Use, Recommended Action, Suggested Critique, and Recommended Next Task.
```

Diff review:

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the diff against the explicit external plan. Identify scope drift, missing verification, and follow-up.
```
