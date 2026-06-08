---
description: Workflow Lite route command for choosing the smallest useful next path.
argument-hint: "Request=<goal or mixed request>; Lens=<optional explicit lenses>"
---

# wf-route

Use Workflow Lite route semantics.

Mode: discuss
Output: compact
Task: route
Lens: ${input:lens:none}

Rules:
- Chat only; do not write files.
- Do not load templates.
- Do not apply lenses automatically; recommend them only.
- Use `.workflow/tasks/route.md` as the task contract.
- Recommend dedicated workflow prompt commands when they fit.
- Use `workflow-lite.prompt.md` as fallback/router for mixed, unclear, or full-protocol requests.

Request:
${input:request:describe the goal or mixed request}

Return:
- Interpreted goal
- Recommended path
- Lens
- Add Context
- Next prompt
