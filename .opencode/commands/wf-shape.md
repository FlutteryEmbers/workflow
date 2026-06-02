---
description: Workflow Lite shape discussion, compact by default.
---

Use Workflow Lite for direction-setting and conceptual design.

Mode: discuss
Output: compact
Task: shape
Lens: none unless explicitly requested

Rules:
- Do not edit files.
- Do not load templates.
- Do not output a full `Persist Packet` unless requested.
- Use `.workflow/tasks/shape.md` as the task contract if needed.
- If evidence is missing and could change the answer, recommend `explore -> shape`.

Request:
$ARGUMENTS

Return:
- Understanding
- Answer, 3-6 bullets
- Risks/Unknowns, 0-3 bullets
- Persist Hint, if worth saving
