---
description: Workflow Lite plan command for repo-aware plans and external-agent handoffs.
argument-hint: "Request=<planning request>; Lens=<none|architecture|boundary|test|language>; Output=<compact|full>"
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
- Treat `execution-candidate` as the terminal complete state for plan: plan-complete enough for review, build executability check, or external-agent handoff.
- A plan may name `Plan Blockers` and next task, but it does not authorize writing, sync, execution, or implementation.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact.
- Use `Output: full` for persisted plans, implementation handoffs, explicit executable plan candidates, or external-agent handoffs.
- Do not output formal `Blocking Gaps` or severity; review owns formal blocking and gap severity.
- Use `Plan Blockers` only when `Plan Readiness: incomplete`.
- Use `Review Focus` only when `Plan Readiness: reviewable | execution-candidate`.
- Use optional `Diagnostic Review Request` when review should diagnose a system or protocol problem for the plan.
- Include `Review Recommended: no | yes | strongly`; review is recommended for material risk but is not a universal build gate.
- For `execution-candidate`, `Recommended Next Task` may be `review`, `build with explicit invocation`, `external-agent`, or `persist`.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
- Shape Summary
- Impact Surface
- Key Changes
- Plan
- Plan Readiness
- Readiness Rationale
- Plan Blockers, only when incomplete
- Review Focus, only when reviewable or execution-candidate
- Diagnostic Review Request, optional
- Recommended Next Task
- Review Recommended
- Next
- Persist Candidate, candidate only and do not write
