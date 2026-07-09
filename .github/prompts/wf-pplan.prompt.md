---
description: "Workflow Lite protected plan command: generate a plan draft, then review the frozen draft for intended next use."
argument-hint: "Request=<planning request>; Intended Next Use=<discussion|persist|build|external-agent|sync>; Lens=<none|architecture|boundary|test|language>"
---

# wf-pplan

Use Workflow Lite as a composite command, not a new task.

Composite:
1. Run `Task: plan` semantics to produce a frozen `Plan Draft`.
2. Run `Task: review` semantics over that frozen draft with `Review Target Kind: plan`.

Mode: discuss
Output: compact
Task: composite plan -> review
Lens: ${input:lens:none}
Intended Next Use: ${input:intended_next_use:discussion}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named for the plan draft; review may use the same selected lens as an extra lens, but plan review itself is core review behavior.
- Use `.workflow/tasks/plan.md` for the first phase.
- Use `.workflow/tasks/review.md` for the second phase.
- Do not ask questions.
- Do not persist, build, sync, execute, or implement.
- Freeze the plan draft before reviewing it; do not revise the plan during the review phase.
- If the plan phase outputs `Input Sufficiency: insufficient`, still review the frozen insufficient result for the requested intended next use.
- The plan draft must use default minimum viable verification unless `Lens: test` is selected or the request explicitly asks for higher assurance.
- If intended next use is `build` or `external-agent`, review whether minimum viable verification, fallback verification, residual risk, and stop conditions are enough for that use; do not mechanically require old baseline, contract freeze, parity matrix, full regression, or e2e.
- Review must output verdict and gaps, not a rewritten plan.

Request:
${input:request:describe the chosen direction and planning need}

Return:
Plan Draft:
- User Intent
- Input Sufficiency
- Input Gaps, only when insufficient
- Shape Summary
- Impact Surface, omitted when insufficient
- Plan At A Glance, omitted when insufficient
- Plan, omitted when insufficient
- Verification: Minimum Viable Verification, Verification Feasibility, Fallback Verification, Residual Risk; omitted when insufficient
- Execution Handoff, only when recommending build or external-agent
- Compatibility / Constraint Plan, when relevant
- Recommended Next Task
- Next

Plan Review:
- Review Frame:
  - Review Question: Can this frozen plan draft be used for the intended next use?
  - Review Target Kind: plan
  - Intended Next Use: discussion | persist | build | external-agent | sync
  - Review Type: verdict-review
  - Review Route Reason: pplan reviews a frozen plan draft instead of revising it.
- Review Verdict: ready | needs changes | needs more evidence | blocked
- Confidence: high | medium | low
- Readiness: 0-10
- Blocking Gaps
- Non-blocking Gaps
- Can Use For Intended Next Use
- Repair Direction
- Recommended Action
- Suggested Critique: explicit redteam critique | none
- Recommended Next Task

Persist Candidate: candidate only and do not write
