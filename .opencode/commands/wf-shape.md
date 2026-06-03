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
- If the user explicitly selects `Lens: conceptual`, output `Planning Level: concept` unless they clearly request another level.

Request:
$ARGUMENTS

Return:
- Understanding
- Planning Level, only when `Lens: conceptual` is selected
- Answer, 3-6 bullets
- Risks/Unknowns, 0-3 bullets
- Persist Hint, if worth saving
