---
description: Workflow Lite plan command for repo-aware plans and external-agent handoffs.
argument-hint: "Request=<planning request>; Lens=<none|architecture|test|language>; Output=<compact|full>"
---

# wf-plan

Use Workflow Lite plan semantics.

Mode: discuss
Output: ${input:output:compact}
Task: plan
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/plan.md` as the task contract.
- Use `Plan Readiness: incomplete | reviewable | execution-candidate` as plan self-assessment.
- A plan may name known gaps and next task, but it does not authorize writing, sync, execution, or implementation.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact.
- Use `Output: full` for persisted plans, implementation handoffs, explicit executable plan candidates, or external-agent handoffs.
- Do not output formal `Blocking Gaps` or severity; review owns formal blocking and gap severity.
- Use `Known Gaps` for plan-owned missing inputs and `Review Focus` for what review should inspect.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
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
