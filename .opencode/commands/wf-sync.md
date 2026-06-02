---
description: Sync Workflow Lite project docs or code-adjacent README.
agent: workflow-docs-sync
---

Use Workflow Lite sync semantics.

Mode: persist
Task: sync
Output: full

Rules:
- Write only `docs/**` or explicit `src/**/README.md`.
- Do not write `.session/**`, `notes/**`, source code, `.workflow/**`, or `.github/**`.
- Require source, future use, source of truth, and future-use success criteria for `docs/**`.
- Use `.workflow/tasks/sync.md` as the task contract if needed.
- If Project Docs Rules are not satisfied, output `docs blocked`.

Request:
$ARGUMENTS

Return:
- Target
- Source of truth
- Changes made or `docs blocked`
- Follow-up
