---
description: Workflow Lite planning draft, repo-aware plan, or external-agent handoff.
---

Use Workflow Lite planning semantics.

Mode: discuss
Output: compact unless handoff detail is required
Task: plan
Lens: none unless explicitly requested

Rules:
- Do not edit files.
- Do not treat this as OpenCode implementation.
- Create a planning draft or repo-aware plan only after the direction is chosen.
- Non-build-ready planning drafts are allowed; label what is missing for implementation handoff.
- Use `Output: full` when the plan is intended as an implementation handoff.
- Use `.workflow/tasks/plan.md` as the task contract if needed.
- Choose `Abstraction Level: phase-plan` or `implementation-plan` automatically unless the user explicitly names one.
- Inherit a source shape artifact's `Recommended Next Abstraction Level` when present; otherwise infer from the request and default to `phase-plan` when uncertain.
- If `implementation-plan` is requested but readiness is incomplete, downgrade to `phase-plan` and list `What Would Make This Implementation-Ready`.

Request:
$ARGUMENTS

Return:
- Target outcome
- Abstraction Level
- Planning Basis
- User Intent
- Current Read, optional
- Take, 3-5 bullets max
- Planning Draft, yes/no
- What Would Make This Implementation-Ready, when relevant
- Risks/Stop conditions
- Next
- Persist Candidate, candidate only and do not write
