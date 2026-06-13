---
description: Workflow Lite shape command for what-if, option-comparison, concept-level, and direction-setting discussion.
argument-hint: "Request=<direction question>; Lens=<none|architecture|language>"
---

# wf-shape

Use Workflow Lite shape semantics.

Mode: discuss
Output: compact
Task: shape
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/shape.md` as the task contract.
- Shape may consume the current chat goal directly or a `.session/inbox/**` brief with `Brief Type: external-goal`.
- Do not require an inbox goal brief when the conversation already contains enough context.
- Provide provisional thinking when useful; do not present it as approval, readiness, or execution permission.
- Put `Decision State` after `Current Read` and before `Take`.
- If `Human Decision State: checkpoint`, output one `User Checkpoint` and stop before `Take`, `Provisional Recommendation`, `Impact Surface`, or `Persist Candidate`.
- If `Human Decision State: blocking`, stop and name the missing evidence or decision.
- If `Human Decision State: assumed`, continue and record the default in `Assumed Decisions`.
- Output `Abstraction Level: concept` when concept structure may feed planning.
- Include `Impact Surface` and `Recommended Next Abstraction Level` when this shape may feed planning.

Request:
${input:request:describe the what-if, option, concept, or direction-setting question}

Return:
- User Intent
- Current Read, optional
- Decision State
- User Checkpoint, only when checkpoint and then stop
- Abstraction Level, when concept structure is relevant
- Take
- Risks/Unknowns
- Provisional Recommendation
- Impact Surface, when planning may follow
- Persist Candidate, candidate only and do not write
