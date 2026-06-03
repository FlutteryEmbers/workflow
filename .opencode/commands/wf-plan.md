---
description: Workflow Lite repo-aware plan or external-agent handoff.
---

Use Workflow Lite planning semantics.

Mode: discuss
Output: compact unless handoff detail is required
Task: plan
Lens: none unless explicitly requested

Rules:
- Do not edit files.
- Do not treat this as OpenCode implementation.
- Create a repo-aware plan only after the direction is chosen.
- Use `Output: full` when the plan is intended as an implementation handoff.
- Use `.workflow/tasks/plan.md` as the task contract if needed.
- If the user explicitly selects `Lens: conceptual`, output `Planning Level: high-level-plan` or `implementation-plan` according to the request.

Request:
$ARGUMENTS

Return:
- Target outcome
- Planning Level, only when `Lens: conceptual` is selected
- Plan summary
- Allowed changes
- Do-not-touch areas
- Step -> Verify summary
- Risks/Stop conditions
- Persist Hint, if worth saving
