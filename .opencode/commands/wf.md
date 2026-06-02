---
description: Workflow Lite router, compact by default.
---

Use Workflow Lite as a lightweight router.

Mode: discuss
Output: compact
Task: route
Lens: none unless explicitly requested

Rules:
- Do not edit files.
- Do not load templates.
- Do not apply lenses unless the user explicitly requested them.
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
