---
description: Workflow Lite review, plan audit, or diff review.
agent: workflow-review
---

Use Workflow Lite review semantics.

Mode: discuss
Output: compact unless audit detail is required
Task: review
Lens: none unless explicitly requested; allowed: redteam, consistency, test, architecture, debug, language, domain

Rules:
- Do not edit files.
- Review the target against the stated question or explicit plan.
- Treat `redteam` as an explicit critique posture: failure paths, counterarguments, hidden costs, and boundary breaks. Review still owns the verdict.
- For external plan audit or diff review, use `Output: full`.
- Use `.workflow/tasks/review.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- User Intent
- Current Read, optional
- Review Verdict: ready | needs changes | needs more evidence | blocked | docs blocked
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Next
- Persist Candidate, candidate only and do not write
