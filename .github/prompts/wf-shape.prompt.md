---
description: Workflow Lite shape command for what-if, option-comparison, concept-level, and direction-setting discussion.
argument-hint: "Request=<direction question>; Lens=<none|architecture|boundary|language|expert>"
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
- Use shape as the small discussion fallback only for concept direction, option framing, and next-step selection.
- Shape may include lightweight clarification, lightweight current-context compression, candidate evidence needs, risk sketch, or non-executable planning sketch when that supports the direction.
- Shape may do a bounded evidence check only to support direction-shaped output. If evidence gathering becomes the main deliverable, route to `explore -> shape`.
- Do not use shape for formal evidence extraction, specified-source summary, formal verdict, source-of-truth judgment, explicit executable plan candidate, stable sync, writes, execution, or implementation.
- Shape may consume the current chat goal directly or a `.session/inbox/**` brief with `Brief Type: external-goal`.
- Do not require an inbox goal brief when the conversation already contains enough context.
- Provide provisional thinking when useful; do not present it as approval, readiness, or execution permission.
- Put `Need For Shape` after `Current Read` and before `Decision State`.
- Use `Need For Shape` only to decide whether shape should continue: `needs-direction | already-settled | answerable-now | needs-evidence | needs-review`.
- If `Need For Shape Status` is not `needs-direction`, stop before `Decision State`, `User Checkpoint`, `Take`, `Provisional Recommendation`, `Impact Surface`, or `Persist Candidate: Artifact=shape`.
- Put `Decision State` after `Need For Shape` and before `Take`, only when `Need For Shape Status: needs-direction`.
- If `Need For Shape Status: needs-direction` and `Human Decision State: checkpoint`, use `vscode/askQuestions` when available.
- Use `User Checkpoint.Question` as the question and `User Checkpoint.Options` as 2-3 mutually exclusive options, preserving label, explanation, and risk. Put the recommended option first and label it `(Recommended)`.
- If that checkpoint UI is unavailable, output one structured `User Checkpoint`.
- After a checkpoint question, stop before `Take`, `Provisional Recommendation`, `Impact Surface`, or `Persist Candidate` until the user chooses.
- If `Human Decision State: blocking`, stop and name the missing evidence or decision.
- If `Human Decision State: assumed`, continue and record the default in `Assumed Decisions`.
- `Need For Shape` is not a review verdict. Do not judge whether code, docs, or plans are correct, solved, ready, or worth changing.
- Keep output at concept level when concept structure may feed planning.
- Include `Impact Surface` and `Recommended Next Task` when this shape may feed planning.
- Do not use `vscode/askQuestions` for unsupported cases.
- Unsupported cases include facts that can be found by preflight or explore, ordinary clarification, review verdicts, planning input gaps, write authorization, sync authorization, or build authorization.

Request:
${input:request:describe the what-if, option, concept, or direction-setting question}

Return:
- User Intent
- Current Read, optional
- Need For Shape
- Boundary Fit
- Adjacent Allowance Used
- Decision State, only when Need For Shape Status is needs-direction
- User Checkpoint, only when checkpoint and then stop
- Take
- Risks/Unknowns
- Provisional Recommendation
- Impact Surface, when planning may follow
- Recommended Next Task
- Persist Candidate, candidate only and do not write
