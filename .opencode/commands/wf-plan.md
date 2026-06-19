---
description: Workflow Lite repo-aware plan or external-agent handoff.
---

Use Workflow Lite planning semantics.

Mode: discuss
Output: compact unless handoff detail is required
Task: plan
Lens: none unless explicitly requested; allowed: architecture, boundary, test, language

Rules:
- Do not edit files.
- Do not treat this as OpenCode implementation.
- Create a repo-aware plan only after the direction is chosen.
- Use `Plan Readiness: incomplete | reviewable | execution-candidate` as plan self-assessment.
- Treat `execution-candidate` as the terminal complete state for plan: plan-complete enough for review, build executability check, or external-agent handoff.
- A plan may name `Plan Blockers` and next task, but it does not authorize writing, sync, execution, or implementation.
- Use `Output: full` when the plan is intended as an implementation handoff.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Plan may run repo-fit preflight for target files, existing patterns, constraints, and verification entrypoints. It must not perform discovery inventory, source-of-truth verdicts, or missing-capability judgments; if repo evidence is missing, output `Plan Readiness: incomplete` and recommend `explore`.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact.
- Use `Output: full` for persisted plans, implementation handoffs, explicit executable plan candidates, or external-agent handoffs.
- Use `.workflow/tasks/plan.md` as the task contract if needed.
- Do not output formal `Blocking Gaps` or severity; review owns formal blocking and gap severity.
- Use `Plan Blockers` only when `Plan Readiness: incomplete`.
- Use `Review Focus` only when `Plan Readiness: reviewable | execution-candidate`.
- Use optional `Diagnostic Review Request` when review should diagnose a system or protocol problem for the plan.
- Include `Review Recommended: no | yes | strongly`; review is recommended for material risk but is not a universal build gate.
- For `execution-candidate`, `Recommended Next Task` may be `review`, `build with explicit invocation`, `external-agent`, or `persist`.

Request:
$ARGUMENTS

Return:
- User Intent
- Target outcome
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
