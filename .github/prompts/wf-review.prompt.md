---
description: Workflow Lite review command for verdicts, gap analysis, audits, diff reviews, plan reviews, and docs/code drift judgment.
argument-hint: "Request=<target to review>; Lens=<none|redteam|consistency|boundary|test|architecture|debug|language|domain|expert>"
---

# wf-review

Use Workflow Lite review semantics.

Mode: discuss
Output: compact
Task: review
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/review.md` as the task contract.
- Output Shape: Review Verdict.
- Always start non-trivial reviews with `Review Frame`.
- `Review Frame` must include `Review Question`, `Review Target Kind`, `Intended Next Use`, `Review Type`, and `Review Route Reason`.
- Use `Review Type: verdict-review | gap-analysis | diff-review`; do not add another review-mode enum.
- User-selected review is respected when a bounded claim can be judged; authority is not expanded.
- Review may perform bounded evidence checks only to support a named verdict or baseline. If evidence mapping becomes the main deliverable, return `needs more evidence` and recommend `explore -> review`.
- Use `gap-analysis` for missing capability, unmet baseline, feature gap, workflow gap, or docs/code alignment gap.
- Use `Change Assessment` only when the user asks whether something should change, is worth changing, has useful improvements, needs adjustment, or asks for improvement audit.
- `Change Assessment` answers change necessity, not review readiness. Use `Answer: yes | no | conditional | unknown`.
- If `Change Assessment Answer: no`, default to `Recommended Action: none` and `Recommended Next Task: none`. If `unknown`, use `Review Verdict: needs more evidence` and recommend `explore`. If `yes` or `conditional`, recommend `shape` when direction is missing and `plan` when direction is selected.
- When `Review Target Kind: plan`, use the built-in plan rubric from `.workflow/tasks/review.md`; do not require a lens for plan review.
- For plan reviews, judge whether the plan can be used for `Intended Next Use`.
- For plan reviews, accept partial or unavailable verification only when fallback verification, residual risk, and stop conditions are explicit enough for the intended use; do not mechanically require contract freeze, old baseline, parity matrix, full regression, or e2e.
- Do not rewrite a plan. Return gaps, `Repair Direction`, and recommended next task.
- Treat `redteam` as an explicit critique posture: failure paths, counterarguments, hidden costs, and boundary breaks. Review still owns the verdict.
- You may include repair direction and minimal revision sketch, but do not create an implementation plan, write, sync, execute, or implement.

Request:
${input:request:describe the plan, diff, docs/code drift, claim, or artifact to review}

Return:
- User Intent
- Current Read, optional
- Review Frame
- Change Assessment, only for change-seeking review
- Baseline, when relevant
- Findings or Take
- Gap Analysis, when Review Type is gap-analysis
- Review Verdict: ready | needs changes | needs more evidence | blocked | docs blocked
- Confidence
- Blocking Gaps
- Non-blocking Gaps
- Can Use For Intended Next Use
- Repair Direction
- Recommended Action
- Recommended Next Task
- Persist Candidate, candidate only and do not write
