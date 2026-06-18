---
description: Workflow Lite route command for choosing the smallest useful next path.
argument-hint: "Request=<goal or mixed request>; Lens=<none; route recommends lenses only>"
---

# wf-route

Use Workflow Lite route semantics.

Mode: discuss
Output: compact
Task: route
Lens: none

Rules:
- Chat only; do not write files.
- Do not load templates.
- Do not apply lenses automatically; recommend them only.
- Do not use any lens as a skip mechanism.
- Use `.workflow/tasks/route.md` as the task contract.
- User-selected task is respected; authority is not expanded.
- If no task fits exactly, use nearest-fit fallback by primary user intent and output `Scope Interpretation`.
- Recommend dedicated workflow prompt commands when they fit.
- Use `workflow-lite.prompt.md` as fallback/router for mixed, unclear, or full-protocol requests.

Request:
${input:request:describe the goal or mixed request}

Return:
- Interpreted goal
- Boundary, when useful
- Scope Interpretation, when using fallback_fit
- Recommended path
- Lens
- Add Context
- Next prompt
