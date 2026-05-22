---
id: review
role: reviewer
purpose: Review behavior, evidence, decisions, plans, diffs, or formal docs alignment.
inputs:
  - target_or_claim
outputs:
  - .session/decisions/dec_{topic}_review.md
user_selectable_lenses:
  - redteam
  - consistency
  - debug
  - distill
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
- `Mode: discuss` is default: review in chat, do not load templates, and do not write files.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize lens views in user-provided order, then give actionable findings.
- `Mode: persist`: write only the requested `.session/decisions/**` target.
- `Mode: execute`: not valid for this task.

## When To Use

- Use when the user asks whether code, docs, a decision, a plan, a diff, or a behavior claim is reasonable.
- Use as a gateway before external-agent implementation and after external-agent diffs.

## Do Not Use When

- Do not use to create a new direction without evaluation; use `shape`.
- Do not use to create implementation steps from an accepted direction; use `plan`.
- Do not use to perform repository edits; use `build` or the external-agent path after approval.
- Do not use to update formal docs; use `sync`.

## Expected Output

- `Mode: discuss`: findings first, then `Decision`, `Confidence`, `Readiness`, blocking gaps, non-blocking gaps, and recommended action.
- `Mode: persist`: a `.session/decisions/**` review decision.

## Task Boundary Check

Before reviewing, classify the request:

- `fits`: user asks to judge code, docs, decisions, plans, diffs, evidence, or reasonableness.
- `fits_with_preflight`: review requires reading code/docs/session context before verdict. In `Mode: discuss`, perform read-only evidence preflight first.
- `wrong_task`: user asks to create a new direction without evaluation; recommend `shape`.
- `wrong_task`: user asks to implement steps from an approved direction; recommend `plan` or `build`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.
- `missing_prerequisite`: `Mode: persist` lacks a `.session/decisions/**` target.

Review acts as a gateway. Verdicts should recommend next task: `none`, `sync`, `shape`, `plan`, `build`, or `external-agent`.

## Persist Templates

- Default: `.workflow/templates/decision.md`
- Review detail: `.workflow/templates/review.md`
- With `redteam`: `.workflow/templates/critique.md`
- With `consistency`: `.workflow/templates/consistency_review.md`

## Copilot Add Context

Required:

- #.workflow/tasks/review.md
- target source, docs, session decision, external plan, diff, behavior claim, or evidence

For `Mode: persist`:

- Add #.workflow/templates/decision.md or the selected review template.
- Provide `Target: .session/decisions/dec_{topic}_review.md`.

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Inspect the target and report findings first. Keep review scope explicit. A review may decide that a session decision is accepted, needs revision, blocked, or should be synced to formal docs.

For non-trivial reviews, include a readiness dashboard:

- `Decision`: `approved | needs changes | blocked | docs blocked | accepted | revise`
- `Confidence`: `high | medium | low`
- `Readiness`: `0-10`
- `Blocking Gaps`: issues that must be resolved before the next write or implementation step.
- `Non-blocking Gaps`: issues that can be tracked without blocking.
- `Recommended Action`: `none | sync | shape | plan | build | external-agent`.

## External Plan Audit

Use this in `Mode: discuss` to audit a native external-agent Plan before implementation.

Decision values:

- `approved`: safe to implement as written.
- `needs changes`: direction is valid but the plan needs edits.
- `blocked`: critical scope, safety, verification, or source-of-truth information is missing.
- `docs blocked`: the plan touches formal docs and source, audience, source of truth, or Formal Docs Rules are unclear.

Check scope, target files, do-not-touch areas, interface or data changes, verification, rollback, open questions, and whether the plan exceeds the user's stated intent.

Also check that success criteria are explicit, every major step has a verification method, and the plan minimizes diff size.

## External Diff Review

Use this in `Mode: discuss` after native external-agent implementation. Compare the diff against the approved external plan and Formal Docs Rules.

Check for scope drift, unrelated edits, missing edits, missing verification, changed files outside the approved target list, and unsafe formal docs content.

Treat drive-by refactors and unapproved scope expansion as blocking unless the approved plan explicitly allowed them.

## Output Rules

- In `Mode: discuss`, report findings in chat only.
- For external plan audit, output `Decision: approved | needs changes | blocked | docs blocked` before detailed findings.
- For external diff review, compare the diff against the approved plan before broader recommendations.
- Default persisted path: `{WorkflowRoot}/.session/decisions/dec_{topic}_review.md`.
- Formal docs fixes go through `sync`.

## User Input

{{code, docs, session decision, external plan, diff, behavior, claim, or risk to review}}
