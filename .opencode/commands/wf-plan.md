---
description: Workflow Lite repo-aware plan or external-agent handoff.
---

Use Workflow Lite planning semantics.

Mode: discuss
Output: compact unless handoff detail is required
Task: plan
Lens: none unless explicitly requested; allowed: architecture, test, language

Rules:
- Do not edit files.
- Do not treat this as OpenCode implementation.
- Create a repo-aware plan only after the direction is chosen.
- Use `Plan Readiness: incomplete | reviewable | execution-candidate` as plan self-assessment.
- A plan may name known gaps and next task, but it does not authorize writing, sync, execution, or implementation.
- Use `Output: full` when the plan is intended as an implementation handoff.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact.
- Use `Output: full` for persisted plans, implementation handoffs, explicit executable plan candidates, or external-agent handoffs.
- Use `.workflow/tasks/plan.md` as the task contract if needed.
- Do not output formal `Blocking Gaps` or severity; review owns formal blocking and gap severity.
- Use `Known Gaps` for plan-owned missing inputs and `Review Focus` for what review should inspect.

Request:
$ARGUMENTS

Return:
- User Intent
- Target outcome
- Shape Summary
- Impact Surface
- Plan
- Plan Readiness
- Readiness Rationale
- Known Gaps
- Review Focus
- Recommended Next Task
- Next
- Persist Candidate, candidate only and do not write
