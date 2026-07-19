---
description: Workflow Lite route command for choosing the smallest useful next path.
argument-hint: "Request=<goal or mixed request>; Lens=<none; route recommends lenses only>"
---

# wf-route

Use Workflow Lite route semantics.

Mode: discuss
Task: route
Lens: none

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
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
- `User Intent`: Interpreted Goal.
- `Task State`: Boundary when useful; Scope Interpretation for fallback fit.
- `Primary Result`: Recommended Path and selected Lens.
- `Supporting Information`: Mode, Write Path, Target, Add Context, segments, and handoff points when relevant.
- `Next`: copyable Next Prompt.
