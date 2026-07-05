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
- Plan must run bounded repo-fit preflight when planning depends on repo facts: target files or areas, existing patterns, constraints, verification entrypoints, and whether the selected direction fits current repo reality. It must not perform discovery inventory, source-of-truth verdicts, or missing-capability judgments; if missing evidence would affect scope, sequence, target files, or verification, output `Plan Readiness: incomplete`, `Plan Blockers`, and recommend `explore`.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact, and include `Motivation`; use `unknown` rather than inventing motivation.
- Use `Output: full` for persisted plans, implementation handoffs, explicit executable plan candidates, or external-agent handoffs.
- Do not output formal `Blocking Gaps` or severity; review owns formal blocking and gap severity.
- Use `Plan Blockers` only when `Plan Readiness: incomplete`; omit it for reviewable and execution-candidate plans.
- Use `Shape Handoff` when direction, motivation, scope, or compatibility decisions belong to `shape`.
- Use `Plan Decision Question` only for one narrow execution-organization choice after the direction is selected; ask at most one question with 2-3 mutually exclusive options, then stop before the plan body until the user chooses. When `vscode/askQuestions` is available, use it to render this question; otherwise output the structured block and wait.
- Use `Review Questions` only when `Plan Readiness: reviewable | execution-candidate`; ask 1-3 concrete questions for review to answer.
- Use optional `Diagnostic Review Request` when review should diagnose a system or protocol problem for the plan.
- Include `Review Recommended: no | yes | strongly`; review is recommended for material risk but is not a universal build gate.
- For `execution-candidate`, `Recommended Next Task` may be `review`, `build`, `external-agent`, or `persist`; write `build with explicit invocation` only in `Next` or `Next Use`.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
- Shape Summary
- Impact Surface
- Plan At A Glance
- Plan
- Plan Readiness
- Readiness Rationale
- Plan Blockers, only when incomplete
- Shape Handoff, only when incomplete because the decision belongs to shape
- Plan Decision Question, only when a single execution-organization choice is needed before planning
- Review Questions, only when reviewable or execution-candidate
- Diagnostic Review Request, optional
- Recommended Next Task
- Review Recommended
- Next
- Persist Candidate, candidate only and do not write
