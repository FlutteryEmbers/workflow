---
description: Workflow Lite router, compact by default.
---

Use Workflow Lite as a lightweight router.

Mode: discuss
Output: compact
Task: route
Lens: none

Rules:
- Do not edit files.
- Do not load templates.
- Do not apply lenses; route may recommend lenses for the next task only.
- Do not use any lens as a skip mechanism.
- Recommend the smallest useful workflow path.
- User-selected task is respected; authority is not expanded.
- If no task fits exactly, use nearest-fit fallback by primary user intent and output `Scope Interpretation`.
- Use `.workflow/tasks/route.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- Interpreted goal
- Boundary, when useful
- Scope Interpretation, when using fallback_fit
- Recommended path
- Lens recommendation, or `none`
- Context to add next
- Next prompt
