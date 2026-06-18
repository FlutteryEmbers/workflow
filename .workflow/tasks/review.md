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
- `Hard Authority Boundaries`: no durable writes, no stable sync, no implementation or write-path execution, no full replacement design, and no implementation plan.

Review output must remain verdict-shaped. It may perform a bounded evidence check over named or directly relevant sources to support the verdict, but it does not output a full discovery inventory.

## Output Shape

`Output Shape: Review Verdict`

Review output is shaped around judgment:

- `Review Question`
- `Evidence Checked`
- `Review Type`
- `Review Verdict`
- `Blocking Gaps`
- `Non-blocking Gaps`
- `Recommended Action`
- `Recommended Next Task`

## Expected Output

- Findings first, then `Review Type`, `Review Verdict`, `Confidence`, `Readiness`, blocking gaps, non-blocking gaps, and recommended action.
- `gap-analysis` output includes baseline, observed state, gap analysis, severity, why it matters, and recommended action.
- `Output: compact` default: short verdict, key findings, and optional `Persist Candidate`.
- `Full Persist Packet` only when the review should be persisted now, used as an audit handoff, or `Output: full` is requested.

## Task Boundary Check

Before reviewing, classify the request. Prefer an in-shape verdict response over `wrong_task` when the user-selected task can still judge a bounded claim.

- `fits`: user asks to judge code, docs, decisions, plans, diffs, evidence, readiness, acceptability, consistency, safety, or reasonableness.
- `fits`: user asks to identify missing capabilities, gaps, drift from expected behavior, or whether a system satisfies a target baseline.
- `fits_with_preflight`: review verdict depends on code, docs, diff, session evidence, or external plan context. In `Mode: discuss`, run conditional implicit preflight first.
- `fallback_fit`: user asks for broad implementation discovery, but review can answer a bounded claim or verdict over named sources.
- `composite`: user asks to review and persist; review first, then route to `persist`.
- `wrong_task`: user asks for writing, stable sync, implementation, or full implementation discovery that cannot be answered as a bounded verdict.

Conditional implicit preflight for `review` only checks review target, review question, and evidence readiness. It must not become open-ended discovery, must not load templates, and must not write files.

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
- `diff-review`: review an implementation diff against an explicit plan.

Use `gap-analysis` when the user asks what is missing, where the gaps are, whether a system satisfies an expected capability, or why a workflow scenario is not supported. Gap analysis requires a `Baseline`. Baseline may come from explicit user goal, protocol purpose, documented promise, expected workflow scenario, or confirmed project standard. If no baseline is available, return `Review Verdict: needs more evidence` or route to `shape`.

Review a plan under `verdict-review` when the question is about plan quality, executability, readiness, fit, risk, or whether the plan can be used as a source for build/external-agent work. When a plan asks review to diagnose whether a system problem exists, choose `verdict-review` or `gap-analysis` based on the review question; do not infer a separate plan-review mode.

Plan-specific missing-input question lists are not a stable review output field. If evidence is insufficient, use `Review Verdict: needs more evidence`, `Blocking Gaps`, or ordinary `Open Questions`.

## Plan Verdict Rules

Use these rules when the review target is a plan:

- `Review Verdict: ready`: no blocking gaps for the plan's intended next use. If the target plan has `Plan Readiness: execution-candidate`, review may return `Review Verdict: ready`, `Blocking Gaps: none`, and `Can Execute Plan: yes`.
- `Review Verdict: needs changes`: the direction is usable, but the plan text must change before the intended next use.
- `Review Verdict: needs more evidence`: evidence or baseline is insufficient to judge the plan.
- `Review Verdict: blocked`: a high-severity blocker prevents the intended next use.

Do not block an `execution-candidate` plan for optional improvement only. Alternative sequencing, style preferences, extra detail, polish, or optional risk reduction belongs in `Non-blocking Gaps`, `Recommended Action`, or `Minimal Revision Sketch` unless it affects target scope, verification, source of truth, compatibility / constraint policy, safety, or executability.

Treat these as typical blocking gaps for plan verdicts: missing target or scope, missing verification, source-of-truth conflict, out-of-scope write risk, unclear compatibility / constraint policy, unsafe docs projection, or steps that cannot be executed without inventing decisions.

Only use `.workflow/lenses/redteam.md` when the user explicitly selects `redteam` or asks for critique, counterarguments, failure paths, or a hostile read. Otherwise, you may output `Suggested Critique: explicit redteam critique` when the target is costly, ambiguous, about to enter execution, or depends on risky assumptions.

Lens use must not change task responsibility. `redteam`, `consistency`, `debug`, `language`, `domain`, `test`, `architecture`, and `expert` may deepen the verdict; `expert` may strengthen findings, evidence pressure, and revision specificity, but must not produce a full replacement design or implementation plan. `review` must not become evidence-only `explore`, full synthesis-oriented `shape`, or executable `plan`.

## Discussion Freedom

In `Mode: discuss`, review may help the human decide what to do next without taking ownership of the redesign.

- You may output `Minimal Revision Sketch` and `Repair Direction` when they make the verdict actionable.
- Keep the sketch minimal: name the direction of change in one sentence, not a full replacement architecture, product direction, implementation sequence, or implementation plan.
- Include `Confidence`, `Assumptions`, and `Human Decision State` when the verdict or repair direction depends on incomplete evidence.
- Do not treat a sketch as approval to write files or execute work.

For non-trivial reviews, include a readiness dashboard:

- `Review Type`: `verdict-review | gap-analysis | diff-review`
- `Review Verdict`: `ready | needs changes | needs more evidence | blocked | docs blocked`
- `Confidence`: `high | medium | low`
- `Readiness`: `0-10`
- `Blocking Gaps`: issues that must be resolved before the next write or implementation step.
- `Non-blocking Gaps`: issues that can be tracked without blocking.
- `Recommended Action`: `none | persist | sync project-docs | sync session-archive | shape | plan | build | external-agent`.
- `Can Execute Plan`: `yes | no | not-applicable`; use only when the review target is a plan.
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

## External Diff Review

Use this in discuss mode after native external-agent implementation. Compare the diff against the explicit external plan and Project Docs Rules.

Check for scope drift, unrelated edits, missing edits, missing verification, changed files outside the plan target list, and unsafe or misleading project docs content.

Treat drive-by refactors and unplanned scope expansion as blocking unless the plan explicitly allowed them.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants reviewed>
Current Read: <optional one line about the target or evidence being reviewed>
Review Type: <verdict-review|gap-analysis|diff-review>
Baseline: <expected state, review question, plan, or none>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Gap Analysis:
- <only for gap-analysis; gap, severity, evidence, impact, why it matters, recommended action>
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
- <verdict-review|gap-analysis|diff-review>
Baseline:
- <expected state, review question, plan, or none>
Refined Verdict:
- <review verdict, key findings, required revisions, and recommended next task>
Gap Analysis:
- <only for gap-analysis; gap, severity, evidence, impact, why it matters, recommended action>
Repair Direction:
- <minimal direction of change, not full redesign>
Discussion Notes To Preserve:
- <review question clarification, evidence priority, accepted risk, verdict change reason, or user concern>
Open Questions:
- <ordinary review uncertainty>
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
- Review Type: <verdict-review | gap-analysis | diff-review>
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
