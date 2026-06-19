---
description: Workflow Lite review command for verdicts, gap analysis, audits, diff reviews, and docs/code drift judgment.
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
- Use verdict-shaped fields: review question, evidence checked, review type, verdict, gaps, readiness, recommended action.
- User-selected review is respected when a bounded claim can be judged; authority is not expanded.
- Use `Review Type: verdict-review | gap-analysis | diff-review`.
- Use `gap-analysis` for missing capability, unmet baseline, feature gap, workflow gap, or docs/code alignment gap.
- Review plans under `verdict-review` when the question is about plan quality, executability, readiness, fit, or risk.
- When a plan asks review to diagnose a system problem, use `verdict-review` or `gap-analysis` based on the question.
- For plan reviews, `Review Verdict: ready` means no blocking gaps for the intended next use. An `execution-candidate` plan may be ready with `Blocking Gaps: none` and `Can Execute Plan: yes`.
- Do not block an execution-candidate plan for optional sequencing improvements, style preferences, polish, or optional risk reduction; put those in `Non-blocking Gaps` or `Minimal Revision Sketch`.
- Treat `redteam` as an explicit critique posture: failure paths, counterarguments, hidden costs, and boundary breaks. Review still owns the verdict.
- Give verdict and minimal repair direction when useful; do not create a full replacement design.
- You may include repair direction and minimal revision sketch, but do not create an implementation plan, write, sync, execute, or implement.

Request:
${input:request:describe the plan, diff, docs/code drift, claim, or artifact to review}

Return:
- User Intent
- Current Read, optional
- Review Type
- Baseline, when relevant
- Findings or Take
- Gap Analysis, when Review Type is gap-analysis
- Review Verdict: ready | needs changes | needs more evidence | blocked | docs blocked
- Confidence
- Blocking Gaps
- Non-blocking Gaps
- Can Execute Plan, when target is a plan
- Minimal Revision Sketch
- Recommended Action
- Recommended Next Task
- Persist Candidate, candidate only and do not write
