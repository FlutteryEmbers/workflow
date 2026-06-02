---
description: Persist Workflow Lite session artifact.
---

Use Workflow Lite persistence semantics.

Mode: persist
Task: persist
Output: full

Rules:
- Write only `.session/**` or explicit `notes/**`.
- Do not write `docs/**`, source code, `.workflow/**`, or `.github/**`.
- Use `.workflow/tasks/persist.md` and the matching `.workflow/templates/<artifact>.md` as contracts if needed.
- You may consume a `Persist Hint`, recent discussion, explicit source files, or user-provided content.
- Preserve decision-relevant reasoning; do not save full transcript by default.

Request:
$ARGUMENTS

Return:
- Target written or proposed
- Artifact kind
- Thread/topic
- Any missing prerequisite
