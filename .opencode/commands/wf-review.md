---
description: Workflow Lite review, plan audit, or diff review.
agent: workflow-review
---

Use Workflow Lite review semantics.

Mode: discuss
Output: compact unless audit detail is required
Task: review
Lens: none unless explicitly requested

Rules:
- Do not edit files.
- Review the target against the stated question or explicit plan.
- For external plan audit or diff review, use `Output: full`.
- Use `.workflow/tasks/review.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- Decision: ready | needs changes | needs more evidence | blocked | docs blocked
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Next
- Persist Hint, none or one line
