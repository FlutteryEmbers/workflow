---
description: Workflow Lite plan command for input-sufficiency-based repo-aware plans and handoffs.
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
- Classify `Input Sufficiency: insufficient | sufficient-for-draft | sufficient-for-handoff`.
- `Input Sufficiency` judges source input for intended use, not the quality of the generated plan.
- Do not ask questions from `plan`; if input is missing, output `Input Gaps` and recommend `shape`, `explore`, `user-answer`, or another `plan` pass.
- Plan must run bounded repo-fit preflight when planning depends on repo facts: target files or areas, existing patterns, constraints, verification entrypoints, and whether the selected direction fits current repo reality.
- If missing evidence would affect scope, sequence, target files, or verification, output `Input Sufficiency: insufficient`, `Input Gaps`, and recommend `explore`.
- Compact plan output must summarize shape/chosen direction, include `Motivation`, and include a compact impact surface when a plan body is output.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact; use `Motivation: unknown` rather than inventing motivation.
- Use `Output: full` for persisted plans, implementation handoffs, explicit handoff candidates, or external-agent handoffs.
- Do not output formal `Blocking Gaps`, severity, review verdicts, or review-style checklists; review owns those.
- For handoff use, include scope, allowed changes, do-not-touch, verification, and stop conditions.
- For build use, `Recommended Next Task` may be `build`, but `Next` must say `build with explicit invocation`.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
- Input Sufficiency
- Input Gaps, only when insufficient
- Shape Summary
- Impact Surface, omitted when insufficient
- Plan At A Glance, omitted when insufficient
- Plan, omitted when insufficient
- Compatibility / Constraint Plan, when relevant
- Recommended Next Task
- Next
- Persist Candidate, candidate only and do not write
