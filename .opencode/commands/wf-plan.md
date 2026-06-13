---
description: Workflow Lite planning draft, repo-aware plan, or external-agent handoff.
---

Use Workflow Lite planning semantics.

Mode: discuss
Output: compact unless handoff detail is required
Task: plan
Lens: none unless explicitly requested; allowed: architecture, test, language

Rules:
- Do not edit files.
- Do not treat this as OpenCode implementation.
- Create a planning draft or repo-aware plan only after the direction is chosen.
- Non-build-ready planning drafts are allowed; label what is missing for implementation handoff.
- Use `Output: full` when the plan is intended as an implementation handoff.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact.
- Use `Output: full` for persisted plans, implementation handoffs, build-ready plans, or external-agent handoffs.
- Use `.workflow/tasks/plan.md` as the task contract if needed.
- Choose `Abstraction Level: phase-plan` or `implementation-plan` automatically unless the user explicitly names one.
- Inherit a source shape artifact's `Recommended Next Abstraction Level` when present; otherwise infer from the request and default to `phase-plan` when uncertain.
- If `implementation-plan` is requested but readiness is incomplete, downgrade to `phase-plan` and list `What Would Make This Implementation-Ready`.
- Replace generic open questions with `Blocking Questions`; use `Follow-up Questions` only in full or refine output.

Request:
$ARGUMENTS

Return:
- User Intent
- Target outcome
- Shape Summary
- Impact Surface
- Plan
- Blocking Questions
- Abstraction Level
- Planning Draft, yes/no
- What Would Make This Implementation-Ready, when relevant
- Next
- Persist Candidate, candidate only and do not write
