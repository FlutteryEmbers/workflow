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
- Shape may consume the current chat goal directly or a `.session/inbox/**` brief with `Brief Type: external-goal`.
- Do not require an inbox goal brief when the conversation already contains enough context.
- If evidence is missing and could change the answer, recommend `explore -> shape`.
- Output `Abstraction Level: concept` for conceptual shape work.
- Include `Impact Surface` and `Recommended Next Abstraction Level` when this shape may feed planning.

Request:
$ARGUMENTS

Return:
- User Intent
- Current Read, optional
- Abstraction Level, when concept structure is relevant
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Impact Surface, when planning may follow
- Next
- Persist Candidate, candidate only and do not write
