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
- Recommend the smallest useful workflow path.
- Use `.workflow/tasks/route.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- Interpreted goal
- Recommended path
- Lens recommendation, or `none`
- Context to add next
- Next prompt
