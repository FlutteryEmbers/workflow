---
description: "Workflow Lite protected plan command: generate a plan draft, then review the frozen draft for intended next use."
argument-hint: "Request=<planning request>; Intended Next Use=<discussion|persist|build|external-agent|sync>; Lens=<none|architecture|boundary|test|language|ponytail>"
---

# wf-pplan

Use Workflow Lite as a composite command, not a new task.

Composite:
1. Run `Task: plan` semantics to produce a frozen `Plan Draft`.
2. Run `Task: review` semantics over that frozen draft with `Review Target Kind: plan`.

Mode: discuss
Task: composite plan -> review
Lens: ${input:lens:none}
Intended Next Use: ${input:intended_next_use:discussion}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named for the plan draft; review may use the same selected lens as an extra lens, but plan review itself is core review behavior.
- Use `.workflow/tasks/plan.md` for the first phase.
- Use `.workflow/tasks/review.md` for the second phase.
- Do not ask ordinary questions. The only exception is a triggered Plan `Compatibility Intake` after repo preflight.
- Do not persist, build, sync, execute, or implement.
- Complete any triggered `Compatibility Intake` before generating or freezing the Plan Draft. Ask consumer scope and data/config lifecycle in one intake round by default; add cutover style only when preflight found an external consumer, persisted data, or material transition cost. Each question has 2-3 mutually exclusive options with the recommended option first. When the third question is omitted, use the preflight-established atomic cutover for mapping. Use `vscode/askQuestions` when available; otherwise output the structured intake with `Input Sufficiency: insufficient` and `Input Gaps: compatibility policy`, then wait without a Plan Draft or Plan Review.
- After compatibility answers are available, generate and freeze the plan draft before reviewing it; do not revise the plan during the review phase.
- If the plan phase outputs `Input Sufficiency: insufficient` for a reason other than unresolved `Compatibility Intake`, still review the frozen insufficient result for the requested intended next use.
- The plan draft must use default minimum viable verification unless `Lens: test` is selected or the request explicitly asks for higher assurance.
- If intended next use is `build` or `external-agent`, review whether minimum viable verification, fallback verification, residual risk, and stop conditions are enough for that use; do not mechanically require old baseline, contract freeze, parity matrix, full regression, or e2e.
- Review must output verdict and gaps, not a rewritten plan.
- With explicit `Lens: ponytail`, apply the same Demo Contract to the frozen plan draft and its review. The plan encodes controlled inputs, relaxed validation, do-not-add constraints, deferred work, upgrade triggers, and minimum proof; the review judges speculative complexity without rewriting the draft.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- `User Intent`: request and intended next use.
- `Task State`: plan Input Sufficiency and Input Gaps when relevant. When a native-UI Compatibility Intake is unavailable, include the discovered evidence and 2-3 questions here, then wait with no Plan Draft or Plan Review.
- `Primary Result`: frozen Plan Draft followed by Plan Review. The draft contains Shape Summary, Impact Surface, Plan At A Glance, Plan, and relevant compatibility decisions; the review contains Review Frame, Review Verdict, and Change Assessment without rewriting the draft.
- `Supporting Information`: draft Verification and Execution Handoff when relevant; review Confidence, Readiness, Blocking Gaps, Non-blocking Gaps, intended-use assessment, Repair Direction, Recommended Action, Suggested Critique, and Recommended Next Task.
- `Next`: explicit next action.
- `Persistence`: Persist Candidate only when worth saving; candidate only and do not write.
