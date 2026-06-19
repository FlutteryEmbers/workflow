---
description: Workflow Lite shape discussion, compact by default.
---

Use Workflow Lite for direction-setting and concept design.

Mode: discuss
Output: compact
Task: shape
Lens: none unless explicitly requested; allowed: architecture, boundary, language, expert

Rules:
- Do not edit files.
- Do not load templates.
- Do not output a full `Persist Packet` unless requested.
- Use `.workflow/tasks/shape.md` as the task contract if needed.
- Use shape as the small discussion fallback only for concept direction, option framing, and next-step selection.
- Shape may include lightweight clarification, lightweight current-context compression, candidate evidence needs, risk sketch, or non-executable planning sketch when that supports the direction.
- Shape may do a bounded evidence check only to support direction-shaped output. If evidence gathering becomes the main deliverable, route to `explore -> shape`.
- Do not use shape for formal evidence extraction, specified-source summary, formal verdict, source-of-truth judgment, explicit executable plan candidate, stable sync, writes, execution, or implementation.
- Shape may consume the current chat goal directly or a `.session/inbox/**` brief with `Brief Type: external-goal`.
- Do not require an inbox goal brief when the conversation already contains enough context.
- If evidence is missing and could change the answer, recommend `explore -> shape`.
- Put `Decision State` after `Current Read` and before `Take`.
- If `Human Decision State: checkpoint`, output one `User Checkpoint` and stop before `Take`, `Impact Surface`, `Next`, or `Persist Candidate`.
- If `Human Decision State: blocking`, stop and name the missing evidence or decision.
- If `Human Decision State: assumed`, continue and record the default in `Assumed Decisions`.
- Keep output at concept level when concept structure may feed planning.
- Include `Impact Surface` and `Recommended Next Task` when this shape may feed planning.

Request:
$ARGUMENTS

Return:
- User Intent
- Current Read, optional
- Boundary Fit
- Adjacent Allowance Used
- Decision State
- User Checkpoint, only when checkpoint and then stop
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Impact Surface, when planning may follow
- Next
- Recommended Next Task
- Persist Candidate, candidate only and do not write
