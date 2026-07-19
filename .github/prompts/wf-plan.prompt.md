---
description: Workflow Lite plan command for input-sufficiency-based repo-aware plans and handoffs.
argument-hint: "Request=<planning request>; Lens=<none|architecture|boundary|test|language|ponytail>"
---

# wf-plan

Use Workflow Lite plan semantics.

Mode: discuss
Task: plan
Lens: ${input:lens:none}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/plan.md` as the task contract.
- Classify `Input Sufficiency: insufficient | sufficient-for-draft | sufficient-for-handoff`.
- `Input Sufficiency` judges source input for intended use, not the quality of the generated plan.
- Do not ask ordinary questions from `plan`; if input is missing, output `Input Gaps` and `Planning Continuation`, recommend `shape`, `explore`, or another `plan` pass, and use `Next: user-answer` when only user-provided input can resolve the gap. The only question exception is a triggered `Compatibility Intake`.
- Plan must run bounded repo-fit preflight when planning depends on repo facts: target files or areas, existing patterns, constraints, verification entrypoints, compatibility surfaces, repository-local consumers, data/config/file-format lifetime, and whether the selected direction fits current repo reality.
- Trigger `Compatibility Intake` only when policy is not locked, a real API/CLI/config/persisted-data/file-format surface exists, preserve versus breaking materially changes scope or verification, and repository evidence cannot determine the external-consumer, data-lifetime, or transition decision. Do not trigger it for explicit policy, no old surface, purely additive behavior, negligible preserve cost, or a repo-determined answer.
- When triggered, use one `vscode/askQuestions` intake round: ask consumer scope and data/config lifecycle, then add cutover style only when preflight found an external consumer, persisted data, or material transition cost. Each question has 2-3 mutually exclusive options; put the recommended option first and mark it `(Recommended)`. When the third question is omitted, use the preflight-established atomic cutover for mapping.
- Map controlled + disposable + atomic to `Compatibility: breaking`; migrate-then-cutover to breaking with migration and old-read/write stop points; a temporary bridge to current `preserve` with a removal trigger and later breaking work; unknown consumers, concurrent formats, or long-term retention to `preserve`. `Lens: ponytail` may recommend demo-only, rebuild, and atomic cutover for controlled/disposable evidence but must not auto-select breaking.
- After native answers, generate the complete Plan. If native UI is unavailable, output `Input Sufficiency: insufficient`, `Input Gaps: compatibility policy`, the evidence-backed structured `Compatibility Intake`, and wait without any plan or handoff body.
- If missing evidence would affect scope, sequence, target files, or any credible minimum verification path, output `Input Sufficiency: insufficient`, `Input Gaps`, `Planning Continuation`, and recommend `explore`.
- Default verification is minimum viable verification: prefer existing fixture/unit/static/smoke/targeted checks, repo scripts, or manual acceptance checks over ideal high-assurance test systems.
- Do not require new test infrastructure, old baseline, contract freeze, full regression, or e2e by default. Treat those as `Higher Assurance` only when `Lens: test` is selected or the user explicitly asks for stronger assurance.
- For refactor or migration without old baseline, output fallback verification and residual risk instead of marking input insufficient solely for that reason.
- The Plan must summarize the shaped/chosen direction, include `Motivation`, and include an impact surface when a plan body is output.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact; use `Motivation: unknown` rather than inventing motivation.
- Do not output formal review-owned gap lists, severity, verdicts, or review-style checklists; review owns those.
- For handoff use, include scope, allowed changes, do-not-touch, minimum viable verification, fallback verification, residual risk, and stop conditions.
- For build use, `Recommended Next Task` may be `build`, but `Next` must say `build with explicit invocation`.
- If the Plan is `sufficient-for-handoff`, it may be used as the explicit implementation or external-agent handoff; recommend persist for durable handoff.
- With explicit `Lens: ponytail`, encode the Demo Contract into existing scope, constraints, verification, stop conditions, and notes: demo path, controlled inputs, relaxed validation, do-not-add fields or abstractions, retained real-world safety, deferred work, upgrade triggers, and minimum proof. Do not infer breaking compatibility or a prototype exception from the lens.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- `User Intent`: request.
- `Task State`: Input Sufficiency; Input Gaps and Planning Continuation only when insufficient; Compatibility Intake only when triggered and native UI is unavailable, in which case omit the plan body and wait.
- `Primary Result`: Shape Summary; Impact Surface, Plan At A Glance, and Plan when sufficient.
- `Supporting Information`: Verification; Execution Handoff only when recommending build or external-agent; Compatibility / Constraint Plan when relevant; Recommended Next Task.
- `Next`: explicit next action.
- `Persistence`: Persist Candidate only when worth saving; candidate only and do not write.
