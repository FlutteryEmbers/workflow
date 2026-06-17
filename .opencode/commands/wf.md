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
- If no task fits exactly, use nearest-fit fallback by primary user intent and output `Boundary Mismatch` plus `Allowed Scope`.
- Use `.workflow/tasks/route.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- Interpreted goal
- Boundary, when useful
- Boundary Mismatch, when using fallback_fit
- Allowed Scope, when using fallback_fit
- Recommended path
- Lens recommendation, or `none`
- Context to add next
- Next prompt
