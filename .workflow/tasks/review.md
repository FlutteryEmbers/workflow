---
id: review
role: reviewer
purpose: Provide verdicts and usability judgments for behavior, evidence, decisions, plans, diffs, source-of-truth, or stable-document alignment in chat.
inputs:
  - target_or_claim
outputs:
  - chat_review
  - persist_hint
user_selectable_lenses:
  - redteam
  - consistency
  - boundary
  - debug
  - language
  - domain
  - test
  - architecture
  - expert
  - ponytail
done_check:
  - findings_are_actionable
  - verdict_target_is_clear
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
- If the user asks to persist, provides a target, or requests an audit artifact, return a `Persist Candidate` and route the write to `persist`; do not construct an intermediate packet.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user asks whether code, docs, a decision, a plan, a diff, or a behavior claim is reasonable, safe, acceptable, usable, consistent, or ready.
- Use when the user asks whether something should change, is worth changing, has useful improvements, or needs adjustment.
- Use when the user asks what capability, behavior, documentation, plan, or system support is missing relative to a baseline; use `Review Type: gap-analysis`.
- Use as a recommended risk/gap audit before external-agent implementation and after external-agent diffs.
- Use inside composite `pplan` to judge a frozen plan draft for an intended next use.

## Do Not Use When

- Do not use to create a new direction without evaluation; use `shape`.
- Do not use for ambiguous what-if, strategy, conceptual, or direction-setting requests unless there is an existing target to judge.
- Do not use to create implementation steps from a chosen direction; use `plan`.
- Do not use to rewrite a plan. Return gaps and `Repair Direction`; route plan repair to `plan`.
- Do not use to perform repository edits; use `build` or the external-agent path after an explicit plan and explicit user invocation.
- Do not use to write session artifacts; use `persist`.
- Do not use to update stable documents; use `sync`.

## Boundary Layers

- `Core Responsibility`: provide a verdict on a target, claim, plan, diff, source-of-truth question, readiness state, or alignment question.
- `Adjacent Allowance`: include minimal revision sketch, repair direction, blocking/non-blocking gaps, suggested critique, and recommended next action when they make the verdict actionable.
- `Hard Authority Boundaries`: no durable writes, no stable sync, no implementation or write-path execution, no full replacement design, no plan rewrite, and no implementation plan.

Review output must remain verdict-shaped. It may perform a bounded evidence check over named or directly relevant sources to support the verdict, but it does not output a full discovery inventory.

## Result Shape

`Output Shape: Review Verdict`

Review output is shaped around judgment:

- `Review Frame`
- `Evidence Checked`
- `Change Assessment`, only for change-seeking review questions
- `Review Verdict`
- `Blocking Gaps`
- `Non-blocking Gaps`
- `Can Use For Intended Next Use`
- `Recommended Action`
- `Recommended Next Task`

## Result Requirements

- Start with user intent, current read when useful, and `Review Frame`; then give findings, `Review Verdict`, `Confidence`, `Readiness`, blocking gaps, non-blocking gaps, and recommended action.
- `gap-analysis` output includes baseline, observed state, gap analysis, severity, why it matters, and recommended action.
- Change-seeking review output includes `Change Assessment`; this is conditional and must not appear as a required dashboard field.
- Plan reviews use the built-in plan rubric; this is core review behavior, not a lens.
- One standard chat response with verdict, material findings, and optional `Persist Candidate`.

## Task Boundary Check

Before reviewing, classify the request. Prefer an in-shape verdict response over `wrong_task` when the user-selected task can still judge a bounded claim.

- `fits`: user asks to judge code, docs, decisions, plans, diffs, evidence, acceptability, consistency, safety, or reasonableness.
- `fits`: user asks whether something should change, has useful improvements, is worth changing, or needs adjustment; use `Change Assessment`.
- `fits`: user asks to identify missing capabilities, gaps, drift from expected behavior, or whether a system satisfies a target baseline.
- `fits_with_preflight`: review verdict depends on code, docs, diff, session evidence, or external plan context. In `Mode: discuss`, run conditional implicit preflight first.
- `fallback_fit`: user asks for broad implementation discovery, but review can answer a bounded claim or verdict over named sources.
- `composite`: user asks to review and persist; review first, then route to `persist`.
- `wrong_task`: user asks for writing, stable sync, implementation, plan creation, or full implementation discovery that cannot be answered as a bounded verdict.
- `wrong_task`: user asks for possible change directions without asking whether change is needed; recommend `shape`.
- `wrong_task`: user asks how to make a chosen change or asks for implementation steps; recommend `plan`.

Conditional implicit preflight for `review` only checks review target, review question, intended next use, and evidence readiness. Review may perform a bounded evidence check only to support a named verdict, claim, diff review, plan review, or baseline. It must not become open-ended discovery, must not load templates, and must not write files.

If evidence is insufficient for a verdict or gap analysis, output `Review Verdict: needs more evidence`, name the missing evidence or missing baseline, and recommend `explore -> review` or `shape` instead of inventing conclusions. If evidence gathering becomes the main deliverable, route to `explore`.

Review provides verdicts and risk/gap judgments when requested or when the selected path uses review. In `Mode: discuss`, those verdicts are thinking material for the user and do not grant write, sync, build, or external-agent authority. Verdicts should recommend next task: `none`, `persist`, `sync`, `shape`, `plan`, `build`, or `external-agent`.

Boundary handling:

- `fits`: review in chat.
- `fits_with_preflight`: run the review-target/evidence preflight, then review or recommend evidence gathering.
- `fallback_fit`: answer the bounded verdict that can be reviewed and name the broader discovery as follow-up.
- `composite`: review first, then output the next-task prompt; do not write files.
- `wrong_task` or `missing_prerequisite`: output `Boundary Advice`. Still provide a bounded review when useful and safe; return boundary-only output only when no bounded verdict can be supported or the request requires write, sync, execution, or another task's authority.

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- target source, docs, session decision, external plan, diff, behavior claim, or evidence

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target, state `Review Frame`, then report findings. Keep review scope explicit. A review may report whether a session artifact is ready, needs changes, lacks evidence, is blocked, or should be synced to stable documents.

Inside `Review Frame`, set `Review Target Kind` explicitly:

- `plan`: plan draft, persisted plan, external plan, or `pplan` frozen draft.
- `diff`: implementation diff or proposed patch.
- `code`: existing code or behavior.
- `docs`: stable docs or code-adjacent README.
- `decision`: session decision, design choice, or compatibility policy.
- `claim`: user or AI claim about behavior, support, readiness, or source of truth.
- `artifact`: session artifact that is not otherwise a plan, diff, code, docs, decision, or claim.

Inside `Review Frame`, set `Intended Next Use` explicitly:

- `discussion`: use as thinking material only.
- `persist`: save as a session artifact.
- `build`: feed workflow-managed build after explicit user invocation.
- `external-agent`: hand to native Plan/Implement or another implementation agent.
- `sync`: project-docs or archive projection.

Every non-trivial review must start with a `Review Frame`:

```text
Review Frame:
- Review Question: <plain-language question this review answers>
- Review Target Kind: plan | diff | code | docs | decision | claim | artifact
- Intended Next Use: discussion | persist | build | external-agent | sync
- Review Type: verdict-review | gap-analysis | diff-review
- Review Route Reason: <why this is review rather than shape, plan, or explore>
```

Use `Review Route Reason` to make routing explicit. For example, "the user is asking whether this is worth changing, not asking for change directions" routes to review; "the user asks for possible directions if changing" routes to shape; "the user asks how to make the chosen change" routes to plan.

Do not create a full replacement design. Route redesign to `shape` and executable sequencing to `plan`. Review may propose required revisions, `Repair Direction`, and a `Minimal Revision Sketch`, but it should not become a design synthesis task or plan rewrite.

Inside `Review Frame`, default review is `Review Type: verdict-review`. Use one primary review type per output:

- `verdict-review`: default correctness, usability, reasonableness, safety, or source-of-truth judgment.
- `gap-analysis`: compare observed state against a baseline and identify missing capability, behavior, coverage, support, or alignment.
- `diff-review`: review an implementation diff against an explicit plan.

Use `gap-analysis` when the user asks what is missing, where the gaps are, whether a system satisfies an expected capability, or why a workflow scenario is not supported. Gap analysis requires a `Baseline`. Baseline may come from explicit user goal, protocol purpose, documented promise, expected workflow scenario, or confirmed project standard. If no baseline is available, return `Review Verdict: needs more evidence` or route to `shape`.

## Change-Seeking Review

Use `Change Assessment` only when the user asks whether something needs change, is worth changing, has useful improvements, should be adjusted, or asks for an improvement audit.

`Change Assessment` answers change necessity. It does not replace `Review Verdict`; the verdict still judges whether the target is usable for the intended next use.

```text
Change Assessment:
- Answer: yes | no | conditional | unknown
- Why: <one sentence>
- Preconditions:
  - <only when Answer is yes or conditional; conditions that make the change worthwhile>
- Suggested Change: <only when Answer is yes or conditional; smallest worthwhile change, not a full design or plan>
```

When `Answer: no`, default to `Recommended Action: none` and `Recommended Next Task: none`. When `Answer: unknown`, default to `Review Verdict: needs more evidence` and recommend `explore`. When `Answer: yes | conditional`, recommend `shape` if a direction choice is still needed and `plan` if the direction is already selected.

Do not list speculative improvements as recommended changes unless their preconditions are true or explicitly assumed. Use `What Is Still Reasonable` to name what can remain unchanged.

## Plan Review Rubric

Use this built-in rubric when `Review Target Kind: plan`. Do not require or invent a plan-review lens.

Judge whether the plan can be used for `Intended Next Use`:

- Source input is sufficient for the intended use.
- Target outcome and scope are explicit.
- Allowed changes and do-not-touch areas are clear when the intended use is build, external-agent, or sync.
- Compatibility and constraint policy are explicit when they affect the work.
- Repo evidence is enough for the planned scope and sequence.
- Sequencing is coherent and does not depend on hidden decisions.
- Verification is concrete enough for the intended use: minimum viable verification, feasibility, fallback verification, residual risk, and acceptance evidence are clear enough to judge the plan.
- Stop conditions are clear enough to prevent scope expansion.
- The plan does not imply unauthorized write, build, sync, or implementation.

Plan review verdicts:

- `Review Verdict: ready`: no blocking gaps for the intended next use.
- `Review Verdict: needs changes`: the direction is usable, but the plan text must change before the intended next use.
- `Review Verdict: needs more evidence`: evidence, source input, or baseline is insufficient to judge the plan.
- `Review Verdict: blocked`: a high-severity gap prevents the intended next use.

Do not block a plan for optional improvement only. Alternative sequencing, style preferences, extra detail, polish, or optional risk reduction belongs in `Non-blocking Gaps`, `Recommended Action`, or `Minimal Revision Sketch` unless it affects scope, verification, source of truth, compatibility / constraint policy, safety, or usability for the intended next use.

For plan reviews, accept `Verification Feasibility: partial | unavailable` when the fallback verification, residual risk, and stop conditions are explicit enough for the intended next use. Do not mechanically require contract freeze, old baseline, parity matrix, full regression, or e2e unless the intended next use, user request, selected lens, safety profile, public API/data risk, or migration risk truly requires that assurance.

Treat these as typical blocking gaps for plan verdicts: missing target or scope, missing verification or fallback verification, source-of-truth conflict, out-of-scope write risk, unclear compatibility / constraint policy, unsafe docs projection, missing allowed changes or do-not-touch areas for handoff use, or steps that cannot be used without inventing decisions.

## Lens Rules

Only use `.workflow/lenses/redteam.md` when the user explicitly selects `redteam` or asks for critique, counterarguments, failure paths, or a hostile read. Otherwise, you may output `Suggested Critique: explicit redteam critique` when the target is costly, ambiguous, about to enter execution, or depends on risky assumptions.

Lens use must not change task responsibility. `redteam`, `consistency`, `boundary`, `debug`, `language`, `domain`, `test`, `architecture`, `expert`, and `ponytail` may deepen the verdict; `boundary` may judge ownership, dependency direction, contract leakage, provider-local business, main-system business, and migration path back to package. `expert` may strengthen findings, evidence pressure, and revision specificity. `ponytail` may judge whether fields, validation, dependencies, abstractions, and compatibility mechanisms have current evidence under a declared Demo Contract, but it must return only findings and the smallest repair direction. It must not produce a full replacement design, implementation plan, or plan rewrite. `review` must not become evidence-only `explore`, full synthesis-oriented `shape`, or executable `plan`.

## Discussion Freedom

In `Mode: discuss`, review may help the human decide what to do next without taking ownership of the redesign.

- You may output `Minimal Revision Sketch` and `Repair Direction` when they make the verdict actionable.
- Keep the sketch minimal: name the direction of change in one sentence, not a full replacement architecture, product direction, implementation sequence, implementation plan, or rewritten plan.
- Include `Confidence`, `Assumptions`, and `Human Decision State` when the verdict or repair direction depends on incomplete evidence.
- Do not treat a sketch as approval to write files or execute work.

For non-trivial reviews, include a readiness dashboard:

- `Review Frame`: question, target kind, intended next use, review type, and route reason.
- `Review Verdict`: `ready | needs changes | needs more evidence | blocked | docs blocked`
- `Confidence`: `high | medium | low`
- `Readiness`: `0-10`
- `Blocking Gaps`: issues that must be resolved before the intended next use.
- `Non-blocking Gaps`: issues that can be tracked without blocking.
- `Recommended Action`: `none | persist | sync project-docs | sync session-archive | shape | plan | build | external-agent`.
- `Can Use For Intended Next Use`: `yes | no | not-applicable`.
- `Suggested Critique`: `explicit redteam critique` or `none`.

## Gap Analysis

Use this in discuss mode to audit missing capability, behavior, coverage, workflow support, docs/code alignment, or plan usability against a baseline.

Gap severity:

- `high`: blocks next write, build, sync, source-of-truth decision, intended plan use, or core workflow scenario.
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

## Response Contract

Use one standard response. Keep the shared groups in order, preserve the
task-specific field names, and omit optional groups that have no content:

```text
User Intent
- <one line about what the user wants reviewed>
Task State
- Current Read: <optional target or evidence summary>
- Boundary Advice: <Boundary, Why, Useful Response Now, Advisory Next Task; omit when it adds no value>
- Review Frame: <Review Question, Review Target Kind, Intended Next Use, Review Type, Review Route Reason>
Primary Result
- Change Assessment: <only for change-seeking review; Answer, Why, Preconditions, Suggested Change>
- Review Verdict: <ready|needs changes|needs more evidence|blocked|docs blocked>
Supporting Information
- Take / Findings: <material findings with evidence and severity when relevant>
- Confidence: <high|medium|low>
- Readiness: <0-10>
- Baseline / Observed State: <for gap analysis when relevant>
- Gap Analysis: <gap, evidence, severity, why it matters, and smallest repair direction>
- Blocking Gaps: <must-fix before intended next use, or none>
- Non-blocking Gaps: <trackable gaps, or none>
- Repair Direction: <smallest repair direction, not a redesign or rewritten plan>
- Minimal Revision Sketch: <only when useful and within review authority>
- Discussion Notes To Preserve: <optional user concern, evidence priority, accepted risk, or verdict-change reason>
- Open Questions: <optional review uncertainty>
- Can Use For Intended Next Use: <yes|no|not-applicable>
- Recommended Action: <none|persist|sync project-docs|sync session-archive|shape|plan|build|external-agent>
- Suggested Critique: <explicit redteam critique|none>
Next
- Recommended Next Task: <shape|plan|build|external-agent|sync|persist|explore|none>
Persistence
- Persist Candidate: Artifact=review; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/review_<topic>.md
```

Omit `Persistence` when the review is not worth preserving. A request for more
detail expands these same fields and all material findings; it does not select
another response mode or load an artifact template.

## User Input

{{code, docs, session decision, external plan, diff, behavior, claim, or risk to review}}
