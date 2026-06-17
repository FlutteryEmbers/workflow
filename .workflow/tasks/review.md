---
id: review
role: reviewer
purpose: Provide verdicts and gatekeeping for behavior, evidence, decisions, plans, diffs, source-of-truth, or stable-document alignment in chat.
inputs:
  - target_or_claim
outputs:
  - chat_review
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - redteam
  - consistency
  - debug
  - language
  - domain
  - test
  - architecture
  - expert
done_check:
  - findings_are_actionable
  - decision_is_clear
  - evidence_is_named
---

# Review Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/reviewer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants reviewed, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize lens views in user-provided order, then give actionable findings.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, requests an audit artifact, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user asks whether code, docs, a decision, a plan, a diff, or a behavior claim is reasonable, safe, acceptable, executable, consistent, or ready.
- Use when the user asks what capability, behavior, documentation, plan, or system support is missing relative to a baseline; use `Review Type: gap-analysis`.
- Use as a recommended risk/gap audit before external-agent implementation and after external-agent diffs.

## Do Not Use When

- Do not use to create a new direction without evaluation; use `shape`.
- Do not use for ambiguous what-if, strategy, conceptual, or direction-setting requests unless there is an existing target to judge.
- Do not use to create implementation steps from a chosen direction; use `plan`.
- Do not use to perform repository edits; use `build` or the external-agent path after an explicit plan.
- Do not use to write session artifacts; use `persist`.
- Do not use to update stable documents; use `sync`.

## Boundary Layers

- `Core Responsibility`: provide a verdict on a target, claim, plan, diff, source-of-truth question, readiness state, or alignment question.
- `Adjacent Allowance`: include minimal revision sketch, repair direction, blocking/non-blocking gaps, suggested critique, and recommended next action when they make the verdict actionable.
- `Forbidden Authority`: do not create a full replacement design, produce an implementation plan, perform evidence-only discovery as the main work, stable-sync documents, write files, execute, or implement.

Adjacent allowance must remain verdict-owned. If the user needs a full redesign, sequencing, or implementation, route to `shape`, `plan`, or `build`/external-agent as the next task.

## Expected Output

- Findings first, then `Review Type`, `Review Verdict`, `Confidence`, `Readiness`, blocking gaps, non-blocking gaps, and recommended action.
- `gap-analysis` output includes baseline, observed state, gap analysis, severity, why it matters, and recommended action.
- `Output: compact` default: short verdict, key findings, and optional `Persist Candidate`.
- `Full Persist Packet` only when the review should be persisted now, used as an audit handoff, or `Output: full` is requested.

## Task Boundary Check

Before reviewing, classify the request:

- `fits`: user asks to judge code, docs, decisions, plans, diffs, evidence, readiness, acceptability, consistency, safety, or reasonableness.
- `fits`: user asks to identify missing capabilities, gaps, drift from expected behavior, or whether a system satisfies a target baseline.
- `fits_with_preflight`: review verdict depends on code, docs, diff, session evidence, or external plan context. In `Mode: discuss`, run conditional implicit preflight first.
- `composite`: user asks to review and persist; review first, then route to `persist`.
- `wrong_task`: user asks to create a new direction without evaluation; recommend `shape`.
- `wrong_task`: user asks to implement steps from a chosen direction; recommend `plan`, or `build` only when an explicit executable plan already exists.
- `wrong_task`: user asks to update project docs, code-adjacent README, or session archive summary; recommend `sync`.

Conditional implicit preflight for `review` only checks review target, review question, and evidence readiness. It must not become a second full review before the review, must not load templates, and must not write files.

If evidence is insufficient for a verdict or gap analysis, output `Review Verdict: needs more evidence`, name the missing evidence or missing baseline, and recommend `explore` or `shape` instead of inventing readiness or blocking conclusions.

Review provides verdicts and risk/gap gates when requested or when the selected path uses review. Verdicts should recommend next task: `none`, `persist`, `sync`, `shape`, `plan`, `build`, or `external-agent`.

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- target source, docs, session decision, external plan, diff, behavior claim, or evidence

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target and report findings first. Keep review scope explicit. A review may report whether a session artifact is ready, needs changes, lacks evidence, is blocked, or should be synced to stable documents.

Do not create a full replacement design. Route redesign to `shape` and executable sequencing to `plan`. Review may propose required revisions, `Repair Direction`, and a `Minimal Revision Sketch`, but it should not become a design synthesis task.

Default review is `Review Type: verdict-review`. Use one primary review type per output:

- `verdict-review`: default correctness, readiness, reasonableness, safety, or source-of-truth judgment.
- `gap-analysis`: compare observed state against a baseline and identify missing capability, behavior, coverage, support, or alignment.
- `plan-audit`: audit a workflow-managed build plan or external-agent plan before implementation.
- `diff-review`: review an implementation diff against an explicit plan.

Use `gap-analysis` when the user asks what is missing, where the gaps are, whether a system satisfies an expected capability, or why a workflow scenario is not supported. Gap analysis requires a `Baseline`. Baseline may come from explicit user goal, protocol purpose, documented promise, expected workflow scenario, or confirmed project standard. If no baseline is available, return `Review Verdict: needs more evidence` or route to `shape`.

Only use `.workflow/lenses/redteam.md` when the user explicitly selects `redteam` or asks for critique, counterarguments, failure paths, or a hostile read. Otherwise, you may output `Suggested Critique: explicit redteam critique` when the target is costly, ambiguous, about to enter execution, or depends on risky assumptions.

Lens use must not change task responsibility. `redteam`, `consistency`, `debug`, `language`, `domain`, `test`, `architecture`, and `expert` may deepen the verdict; `expert` may strengthen findings, evidence pressure, and revision specificity, but must not produce a full replacement design or implementation plan. `review` must not become evidence-only `explore`, full synthesis-oriented `shape`, or executable `plan`.

## Discussion Freedom

In `Mode: discuss`, review may help the human decide what to do next without taking ownership of the redesign.

- You may output `Minimal Revision Sketch` and `Repair Direction` when they make the verdict actionable.
- Keep the sketch minimal: name the direction of change in one sentence, not a full replacement architecture, product direction, implementation sequence, or implementation plan.
- Include `Confidence`, `Assumptions`, and `Human Decision State` when the verdict or repair direction depends on incomplete evidence.
- Do not treat a sketch as approval to write files or execute work.

For non-trivial reviews, include a readiness dashboard:

- `Review Type`: `verdict-review | gap-analysis | plan-audit | diff-review`
- `Review Verdict`: `ready | needs changes | needs more evidence | blocked | docs blocked`
- `Confidence`: `high | medium | low`
- `Readiness`: `0-10`
- `Blocking Gaps`: issues that must be resolved before the next write or implementation step.
- `Non-blocking Gaps`: issues that can be tracked without blocking.
- `Recommended Action`: `none | persist | sync project-docs | sync session-archive | shape | plan | build | external-agent`.
- `Suggested Critique`: `explicit redteam critique` or `none`.

## Gap Analysis

Use this in discuss mode to audit missing capability, behavior, coverage, workflow support, docs/code alignment, or plan readiness against a baseline.

Gap severity:

- `high`: blocks next write, build, sync, source-of-truth decision, or core workflow scenario.
- `medium`: does not block immediately but creates material rework, ambiguity, drift, user friction, or maintenance risk.
- `low`: clarity, polish, convenience, or non-blocking completeness issue.

Each gap must include:

```text
Gap Analysis:
- Gap: <missing capability, behavior, evidence, or alignment>
  Severity: <high|medium|low>
  Evidence: <observed evidence>
  Impact: <what scenario, user, workflow, or handoff is affected>
  Why It Matters: <why this gap should or should not be solved now>
  Recommended Action: <repair direction, next task, or none>
  Recommended Next Task: <shape|explore|plan|sync|build|external-agent|persist|none>
```

`Blocking Gaps` derives from `high` severity. `Non-blocking Gaps` derives from `medium` and `low` severity. Do not use severity as a replacement for findings or evidence.

## Plan Audit

Use this in discuss mode when `Review Type: plan-audit` to audit a workflow-managed `build` plan or an external-agent plan before implementation.

Decision values:

- `ready`: safe to implement as written.
- `needs changes`: direction is valid but the plan needs edits.
- `blocked`: critical scope, safety, verification, or source-of-truth information is missing.
- `docs blocked`: the target touches project docs and source, scope, sync object, source of truth, target selection, alignment success criteria, or Project Docs Rules are unclear.

Plan audit checks whether a plan is understandable, bounded, executable, and reviewable enough for its intended next use. It does not rewrite the plan, choose a new direction, create a replacement design, sequence implementation, or produce an implementation plan.

Audit dimensions:

- Intent Fit: does the plan match the user goal and selected direction?
- Scope Boundary: are target files or areas, allowed changes, and do-not-touch areas clear?
- Executability: can `build` or an external agent execute without inventing product decisions?
- Verification: does each major step have a verification method or accepted verification gap?
- Stop Conditions: does the plan say when to stop instead of expanding scope?
- Compatibility / Constraint Policy: are breaking changes and constraint exceptions explicit?
- Risk Posture: does `Review Recommended` or risk level match the actual impact surface?
- Known Gaps Escalation: should any `Known Gaps` become formal `Blocking Gaps`?
- Review Focus Coverage: does `Review Focus` cover the actual risks review should inspect?

For `plan-audit`, output `Blocking Questions` when the plan is not ready for its intended next use. These questions must be user-answerable and must describe missing input, not prescribe a solution.

```text
Blocking Questions:
- Question: <user-answerable question>
  Severity: <high|medium|low>
  Blocks: <build|external-agent|sync|source-of-truth decision|plan persistence|none>
  Evidence: <where the plan is missing, weak, or contradictory>
  Impact: <what could go wrong or what cannot proceed>
  Why It Matters: <why the user should answer this before proceeding>
  Answer Needed: <decision, fact, boundary, evidence, or source needed>
  Recommended Next Task: <shape|explore|plan|review|build|external-agent|sync|persist|none>
```

`Answer Needed` must describe the missing input required to answer the question. It must not propose a design, choose a direction, sequence implementation, or rewrite the plan. Route those needs to `shape` or `plan`.

Allowed `Answer Needed` examples:

- `name the target files or target modules`
- `confirm whether compatibility may be breaking`
- `provide the command or manual check that proves success`
- `choose source of truth between the plan and docs/foo.md`

Forbidden `Answer Needed` examples:

- `split routing into three modules`
- `use strategy A instead of strategy B`
- `implement adapter X first, then migrate templates`
- `redesign the persistence model`

Plan-audit severity:

- `high`: blocks build, external-agent implementation, sync, source-of-truth decision, or risks out-of-scope writes.
- `medium`: does not block immediate discussion, but creates material rework, ambiguity, drift, or verification weakness.
- `low`: clarity, polish, convenience, or non-blocking completeness issue.

`Blocking Gaps` derives from high-severity `Blocking Questions` and high-severity findings. `Non-blocking Gaps` derives from medium/low questions and findings. If all questions are medium/low and the user explicitly invokes `build`, treat them as risk notices rather than universal hard blockers; `build` still performs executability validation.

Plan audit owns formal blocking. Plans may provide `Plan Readiness`, `Known Gaps`, `Review Focus`, and `Review Recommended`, but review decides `Blocking Questions`, `Blocking Gaps`, severity, and whether the plan can proceed.

## External Diff Review

Use this in discuss mode after native external-agent implementation. Compare the diff against the explicit external plan and Project Docs Rules.

Check for scope drift, unrelated edits, missing edits, missing verification, changed files outside the plan target list, and unsafe or misleading project docs content.

Treat drive-by refactors and unplanned scope expansion as blocking unless the plan explicitly allowed them.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants reviewed>
Current Read: <optional one line about the target or evidence being reviewed>
Review Type: <verdict-review|gap-analysis|plan-audit|diff-review>
Baseline: <expected state, review question, plan, or none>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Gap Analysis:
- <only for gap-analysis; gap, severity, evidence, impact, why it matters, recommended action>
Blocking Questions:
- <only for plan-audit; question, severity, blocks, evidence, impact, why it matters, answer needed>
Minimal Revision Sketch: <smallest repair direction or none>
Recommended Next Task: <shape|plan|build|external-agent|sync|persist|explore|none>
Persist Candidate: Artifact=review; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/review_<topic>.md
```

Use `Persist Candidate: none` when the review is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare the review for persist without writing files:

```text
User Intent: <one line about what the user wants reviewed>
Current Read: <optional one line about the target or evidence being reviewed>
Review Type:
- <verdict-review|gap-analysis|plan-audit|diff-review>
Baseline:
- <expected state, review question, plan, or none>
Refined Verdict:
- <review verdict, key findings, required revisions, and recommended next task>
Gap Analysis:
- <only for gap-analysis; gap, severity, evidence, impact, why it matters, recommended action>
Blocking Questions:
- <only for plan-audit; question, severity, blocks, evidence, impact, why it matters, answer needed>
Repair Direction:
- <minimal direction of change, not full redesign>
Discussion Notes To Preserve:
- <review question clarification, evidence priority, accepted risk, verdict change reason, or user concern>
Open Questions:
- <ordinary review uncertainty; do not use this for plan-audit formal blocking questions>
Recommended Next Task:
- <shape|plan|build|external-agent|sync|persist|explore|none>
Persist Candidate:
- Artifact=review; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/review_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs an audit/diff-review artifact. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load `.workflow/templates/review.md` or `.workflow/templates/decision.md` and shape the final artifact.

```text
Persist Packet:
Artifact: review | decision
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/review_<topic>.md
Source Summary: <plan, diff, code, docs, session artifact, or claim reviewed>
Key Fields:
- Review Type: <verdict-review | gap-analysis | plan-audit | diff-review>
- Review Question: <what was being judged>
- Baseline: <expected state, documented promise, user goal, workflow scenario, or none>
- Review Verdict: <ready | needs changes | needs more evidence | blocked | docs blocked>
- Findings: <key findings with severity and evidence>
- Gap Summary: <blocking and non-blocking gaps, or none>
- Recommended Action: <repair direction, next task, or none>
Next Use: <persist | shape | plan | build | sync | none>
```

If the review is not worth preserving, output `Persist Candidate: none`.

## User Input

{{code, docs, session decision, external plan, diff, behavior, claim, or risk to review}}
