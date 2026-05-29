---
id: review
role: reviewer
purpose: Provide verdicts and gatekeeping for behavior, evidence, decisions, plans, diffs, or formal docs alignment in chat.
inputs:
  - target_or_claim
outputs:
  - chat_review
  - save_packet
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

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize lens views in user-provided order, then give actionable findings.
- Do not load templates and do not write files.
- If the user asks to save or provides a target, return `Save Packet` and route the write to `save`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user asks whether code, docs, a decision, a plan, a diff, or a behavior claim is reasonable, safe, acceptable, executable, consistent, or ready.
- Use as a gateway before external-agent implementation and after external-agent diffs.

## Do Not Use When

- Do not use to create a new direction without evaluation; use `shape`.
- Do not use for ambiguous what-if, strategy, conceptual, or direction-setting requests unless there is an existing target to judge.
- Do not use to create implementation steps from an accepted direction; use `plan`.
- Do not use to perform repository edits; use `build` or the external-agent path after approval.
- Do not use to write session artifacts; use `save`.
- Do not use to update formal docs; use `sync`.

## Expected Output

- Findings first, then `Decision`, `Confidence`, `Readiness`, blocking gaps, non-blocking gaps, and recommended action.
- `Save Packet` when the review should become a draft review or accepted verdict.

## Task Boundary Check

Before reviewing, classify the request:

- `fits`: user asks to judge code, docs, decisions, plans, diffs, evidence, readiness, acceptability, consistency, safety, or reasonableness.
- `fits_with_preflight`: review verdict depends on code, docs, diff, session evidence, or external plan context. In `Mode: discuss`, run conditional implicit preflight first.
- `composite`: user asks to review and save; review first, then route to `save`.
- `wrong_task`: user asks to create a new direction without evaluation; recommend `shape`.
- `wrong_task`: user asks to implement steps from an approved direction; recommend `plan` or `build`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.

Conditional implicit preflight for `review` only checks review target, review question, and evidence readiness. It must not become a second full review before the review, must not load templates, and must not write files.

If evidence is insufficient for a verdict, output `Decision: needs more evidence`, name the missing evidence, and recommend `explore` instead of inventing approval or blocking conclusions.

Review acts as a gateway. Verdicts should recommend next task: `none`, `save`, `sync`, `shape`, `plan`, `build`, or `external-agent`.

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- target source, docs, session decision, external plan, diff, behavior claim, or evidence

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target and report findings first. Keep review scope explicit. A review may decide that a session artifact is accepted, needs revision, blocked, or should be synced to formal docs.

Do not invent a replacement design. Route redesign to `shape` and executable sequencing to `plan`. Review may propose required revisions and next task, but it should not become a design synthesis task.

Default review is normal review. Only use `.workflow/lenses/redteam.md` when the user explicitly selects `redteam`. Otherwise, you may output `Suggested Lens: redteam` when the target is costly, ambiguous, about to enter execution, or depends on risky assumptions.

Lens use must not change task responsibility. `redteam`, `consistency`, `debug`, `language`, `domain`, `test`, and `architecture` may deepen the verdict, but `review` must not become evidence-only `explore`, synthesis-oriented `shape`, or executable `plan`.

For non-trivial reviews, include a readiness dashboard:

- `Decision`: `approved | needs changes | needs more evidence | blocked | docs blocked | accepted | revise`
- `Confidence`: `high | medium | low`
- `Readiness`: `0-10`
- `Blocking Gaps`: issues that must be resolved before the next write or implementation step.
- `Non-blocking Gaps`: issues that can be tracked without blocking.
- `Recommended Action`: `none | save | sync | shape | plan | build | external-agent`.
- `Suggested Lens`: `redteam` or `none`.

## External Plan Audit

Use this in discuss mode to audit a native external-agent Plan before implementation.

Decision values:

- `approved`: safe to implement as written.
- `needs changes`: direction is valid but the plan needs edits.
- `blocked`: critical scope, safety, verification, or source-of-truth information is missing.
- `docs blocked`: the plan touches formal docs and source, audience, source of truth, or Formal Docs Rules are unclear.

Check scope, target files, do-not-touch areas, interface or data changes, verification, rollback, open questions, and whether the plan exceeds the user's stated intent.

Also check that success criteria are explicit, every major step has a verification method, and the plan minimizes diff size.

## External Diff Review

Use this in discuss mode after native external-agent implementation. Compare the diff against the approved external plan and Formal Docs Rules.

Check for scope drift, unrelated edits, missing edits, missing verification, changed files outside the approved target list, and unsafe formal docs content.

Treat drive-by refactors and unapproved scope expansion as blocking unless the approved plan explicitly allowed them.

## Save Packet

When useful, end with:

```text
Save Packet:
Artifact: review | decision
Status: draft | accepted
Intent: audit | decision
Depth: detailed
Topic: <topic>
Suggested Target: .session/drafts/review_<topic>.md or .session/accepted/review_<topic>.md
Source Context:
- <plan, diff, code, docs, session artifact, or claim reviewed>
Review Question:
- <what was being judged>
Evidence Checked:
- <files, docs, diffs, artifacts, or discussion evidence>
Key Points:
- <verdict and main reasons>
Decision-Relevant Facts:
- <facts that affect approval, revision, or blocking>
Reasoning Trail:
- <concern -> evidence -> verdict>
Findings:
- <finding with severity and evidence>
What Is Still Reasonable:
- <parts that should be preserved>
Required Revisions:
- <must-change items>
Rejected Options:
- <fixes or interpretations rejected>
Risks / Unknowns:
- <remaining risk or missing evidence>
Examples / Pseudocode:
- <example failure path or reproduction if useful>
Recommended Next Task:
- <shape | plan | build | sync | save | none>
Next Use:
- <save draft | save accepted | plan | build | sync>
```

Accepted review verdicts require explicit accepted, approved, or promote intent.
If the review is not worth preserving, output `Save Packet: none`.

## User Input

{{code, docs, session decision, external plan, diff, behavior, claim, or risk to review}}
