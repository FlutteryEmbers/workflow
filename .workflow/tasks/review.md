---
id: review
role: reviewer
purpose: Provide verdicts and gatekeeping for behavior, evidence, decisions, plans, diffs, source-of-truth, or project docs alignment in chat.
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
- Use as a gateway before external-agent implementation and after external-agent diffs.

## Do Not Use When

- Do not use to create a new direction without evaluation; use `shape`.
- Do not use for ambiguous what-if, strategy, conceptual, or direction-setting requests unless there is an existing target to judge.
- Do not use to create implementation steps from a chosen direction; use `plan`.
- Do not use to perform repository edits; use `build` or the external-agent path after an explicit plan.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.

## Expected Output

- Findings first, then `Review Verdict`, `Confidence`, `Readiness`, blocking gaps, non-blocking gaps, and recommended action.
- `Output: compact` default: short verdict, key findings, and optional `Persist Candidate`.
- `Full Persist Packet` only when the review should be persisted now, used as an audit handoff, or `Output: full` is requested.

## Task Boundary Check

Before reviewing, classify the request:

- `fits`: user asks to judge code, docs, decisions, plans, diffs, evidence, readiness, acceptability, consistency, safety, or reasonableness.
- `fits_with_preflight`: review verdict depends on code, docs, diff, session evidence, or external plan context. In `Mode: discuss`, run conditional implicit preflight first.
- `composite`: user asks to review and persist; review first, then route to `persist`.
- `wrong_task`: user asks to create a new direction without evaluation; recommend `shape`.
- `wrong_task`: user asks to implement steps from a chosen direction; recommend `plan` or `build`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

Conditional implicit preflight for `review` only checks review target, review question, and evidence readiness. It must not become a second full review before the review, must not load templates, and must not write files.

If evidence is insufficient for a verdict, output `Review Verdict: needs more evidence`, name the missing evidence, and recommend `explore` instead of inventing readiness or blocking conclusions.

Review acts as a gateway. Verdicts should recommend next task: `none`, `persist`, `sync`, `shape`, `plan`, `build`, or `external-agent`.

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- target source, docs, session decision, external plan, diff, behavior claim, or evidence

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target and report findings first. Keep review scope explicit. A review may report whether a session artifact is ready, needs changes, lacks evidence, is blocked, or should be synced to code-aligned project docs.

Do not create a full replacement design. Route redesign to `shape` and executable sequencing to `plan`. Review may propose required revisions, `Repair Direction`, and a `Minimal Revision Sketch`, but it should not become a design synthesis task.

Default review is normal review. Only use `.workflow/lenses/redteam.md` when the user explicitly selects `redteam`. Otherwise, you may output `Suggested Lens: redteam` when the target is costly, ambiguous, about to enter execution, or depends on risky assumptions.

Lens use must not change task responsibility. `redteam`, `consistency`, `debug`, `language`, `domain`, `test`, and `architecture` may deepen the verdict, but `review` must not become evidence-only `explore`, full synthesis-oriented `shape`, or executable `plan`.

## Discussion Freedom

In `Mode: discuss`, review may help the human decide what to do next without taking ownership of the redesign.

- You may output `Minimal Revision Sketch` and `Repair Direction` when they make the verdict actionable.
- Keep the sketch minimal: name the direction of change, not a full replacement architecture or implementation plan.
- Include `Confidence`, `Assumptions`, and `Human Decision Needed` when the verdict or repair direction depends on incomplete evidence.
- Do not treat a sketch as approval to write files or execute work.

For non-trivial reviews, include a readiness dashboard:

- `Review Verdict`: `ready | needs changes | needs more evidence | blocked | docs blocked`
- `Confidence`: `high | medium | low`
- `Readiness`: `0-10`
- `Blocking Gaps`: issues that must be resolved before the next write or implementation step.
- `Non-blocking Gaps`: issues that can be tracked without blocking.
- `Recommended Action`: `none | persist | sync | shape | plan | build | external-agent`.
- `Suggested Lens`: `redteam` or `none`.

## External Plan Audit

Use this in discuss mode to audit a native external-agent Plan before implementation.

Decision values:

- `ready`: safe to implement as written.
- `needs changes`: direction is valid but the plan needs edits.
- `blocked`: critical scope, safety, verification, or source-of-truth information is missing.
- `docs blocked`: the target touches project docs and source, scope, docs type, source of truth, alignment success criteria, or Project Docs Rules are unclear.

Check scope, target files, do-not-touch areas, interface or data changes, verification, rollback, open questions, and whether the plan exceeds the user's stated intent.

Also check that success criteria are explicit, every major step has a verification method, and the plan minimizes diff size.

## External Diff Review

Use this in discuss mode after native external-agent implementation. Compare the diff against the explicit external plan and Project Docs Rules.

Check for scope drift, unrelated edits, missing edits, missing verification, changed files outside the plan target list, and unsafe or misleading project docs content.

Treat drive-by refactors and unplanned scope expansion as blocking unless the plan explicitly allowed them.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants reviewed>
Current Read: <optional one line about the target or evidence being reviewed>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Minimal Revision Sketch: <smallest repair direction or none>
Persist Candidate: Artifact=review; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/review_<topic>.md
```

Use `Persist Candidate: none` when the review is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to整理, refine, or prepare the review for persist without writing files:

```text
User Intent: <one line about what the user wants reviewed>
Current Read: <optional one line about the target or evidence being reviewed>
Refined Direction / Plan:
- <review verdict, key findings, required revisions, and recommended next task>
Repair Direction:
- <minimal direction of change, not full redesign>
Discussion Notes To Preserve:
- <review question clarification, evidence priority, accepted risk, verdict change reason, or user concern>
Open Questions:
- <question that blocks readiness, execution, or sync>
Persist Candidate:
- Artifact=review; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/review_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs an audit/diff-review artifact:

```text
Persist Packet:
Artifact: review | decision
Artifact State: working | settled | superseded
Intent: audit | decision
Depth: detailed
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/review_<topic>.md
Source Context:
- <plan, diff, code, docs, session artifact, or claim reviewed>
Review Question:
- <what was being judged>
Evidence Checked:
- <files, docs, diffs, artifacts, or discussion evidence>
Key Points:
- <verdict and main reasons>
Decision-Relevant Facts:
- <facts that affect readiness, revision, or blocking>
Decision Trail:
- <concern -> evidence -> verdict>
Findings:
- <finding with severity and evidence>
What Is Still Reasonable:
- <parts that should be preserved>
Required Revisions:
- <must-change items>
Minimal Revision Sketch:
- <smallest repair direction; route full redesign to shape and sequencing to plan>
Rejected Options:
- <fixes or interpretations rejected>
Risks / Unknowns:
- <remaining risk or missing evidence>
Examples / Pseudocode:
- <example failure path or reproduction if useful>
Recommended Next Task:
- <shape | plan | build | sync | persist | none>
Next Use:
- <persist | plan | build | sync>
```

If the review is not worth preserving, output `Persist Candidate: none`.

## User Input

{{code, docs, session decision, external plan, diff, behavior, claim, or risk to review}}
