---
description: Workflow Lite review, gap analysis, or diff review.
agent: workflow-review
---

Use Workflow Lite review semantics.

Mode: discuss
Output: compact unless audit detail is required
Task: review
Lens: none unless explicitly requested; allowed: redteam, consistency, boundary, test, architecture, debug, language, domain, expert

Rules:
- Do not edit files.
- Review the target against the stated question or explicit plan.
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
- You may include repair direction and minimal revision sketch, but do not create an implementation plan, write, sync, execute, or implement.
- For external plan review or diff review, use `Output: full`.
- Use `.workflow/tasks/review.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- User Intent
- Current Read, optional
- Review Type
- Baseline, when relevant
- Review Verdict: ready | needs changes | needs more evidence | blocked | docs blocked
- Gap Analysis, when Review Type is gap-analysis
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Blocking Gaps
- Non-blocking Gaps
- Can Execute Plan, when target is a plan
- Minimal Revision Sketch
- Recommended Next Task
- Next
- Persist Candidate, candidate only and do not write
