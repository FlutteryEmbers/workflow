---
description: Workflow Lite shape command for what-if, option-comparison, concept-level, and direction-setting discussion.
argument-hint: "Request=<direction question>; Lens=<none|architecture|boundary|language|expert|ponytail>"
---

# wf-shape

Use Workflow Lite shape semantics.

Mode: discuss
Task: shape
Lens: ${input:lens:none}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
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
- Put `Need For Shape` after `Current Read` and before `Shape Continuation`.
- Use `Need For Shape` as an advisory continuation classifier: `needs-direction | already-settled | answerable-now | needs-evidence | needs-review`.
- Always include `Shape Continuation`. When status is not `needs-direction`, provide carry-forward context, a short answer, provisional direction, or review handoff instead of creating a new direction by default.
- Put `Decision State` after `Shape Continuation` and before finalized recommendation when a user-owned choice matters.
- If `Need For Shape Status: needs-direction` and `Human Decision State: checkpoint`, use `vscode/askQuestions` when available.
- Use `User Checkpoint.Question` as the question and `User Checkpoint.Options` as 2-3 mutually exclusive options, preserving label, explanation, and risk. Put the recommended option first and label it `(Recommended)`.
- If that checkpoint UI is unavailable, output one structured `User Checkpoint`.
- After a checkpoint question, wait for the user selection before finalizing `Take`, `Provisional Recommendation`, `Impact Surface`, or `Persist Candidate`.
- If `Human Decision State: unresolved`, continue with provisional direction, assumptions, what would change it, and advisory next task.
- If `Human Decision State: assumed`, continue and record the default in `Assumed Decisions`.
- `Need For Shape` is not a review verdict. Do not judge whether code, docs, or plans are correct, solved, ready, or worth changing.
- Keep output at concept level when concept structure may feed planning.
- Include `Impact Surface` and `Recommended Next Task` when this shape may feed planning.
- With explicit `Lens: ponytail`, define the smallest credible demo path, controlled inputs, required-now behavior, relaxed production validation, rejected larger scope, deferred work, and upgrade triggers. Keep the output concept-level and do not infer breaking compatibility or a prototype exception from the lens.
- Do not use `vscode/askQuestions` for unsupported cases.
- Unsupported cases include facts that can be found by preflight or explore, ordinary clarification, review verdicts, planning input gaps, write authorization, sync authorization, or build authorization.

Request:
${input:request:describe the what-if, option, concept, or direction-setting question}

Return:
- `User Intent`: shaping request.
- `Task State`: Current Read, Need For Shape, Shape Continuation, Boundary Advice, Adjacent Allowance Used, Decision State, and User Checkpoint only when triggered; wait after the checkpoint.
- `Primary Result`: Reframed Goal, Take / Refined Direction, Provisional Recommendation, Narrowest Useful Wedge, success criteria, and Rejected Larger Scope when relevant.
- `Supporting Information`: risks, change conditions, Impact Surface, decisions, compatibility/constraint check, notes, and open questions.
- `Next`: Recommended Next Task.
- `Persistence`: Persist Candidate only when the response forms or updates a direction; omit while a checkpoint waits and do not write.
