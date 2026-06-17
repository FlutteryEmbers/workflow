---
description: Workflow Lite review, gap analysis, plan audit, or diff review.
agent: workflow-review
---

Use Workflow Lite review semantics.

Mode: discuss
Output: compact unless audit detail is required
Task: review
Lens: none unless explicitly requested; allowed: redteam, consistency, test, architecture, debug, language, domain, expert

Rules:
- Do not edit files.
- Review the target against the stated question or explicit plan.
- Use `Review Type: verdict-review | gap-analysis | plan-audit | diff-review`.
- Use `gap-analysis` for missing capability, unmet baseline, feature gap, workflow gap, or docs/code alignment gap.
- For `Review Type: plan-audit`, include `Blocking Questions` with severity, blocks, evidence, impact, why it matters, `Answer Needed`, and recommended next task.
- `Answer Needed` must describe missing input only; do not propose a design, choose a direction, sequence implementation, or rewrite the plan.
- Treat `redteam` as an explicit critique posture: failure paths, counterarguments, hidden costs, and boundary breaks. Review still owns the verdict.
- You may include repair direction and minimal revision sketch, but do not create an implementation plan, write, sync, execute, or implement.
- For external plan audit or diff review, use `Output: full`.
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
- Blocking Questions, when Review Type is plan-audit
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Blocking Gaps
- Non-blocking Gaps
- Minimal Revision Sketch
- Recommended Next Task
- Next
- Persist Candidate, candidate only and do not write
