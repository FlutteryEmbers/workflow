---
description: Workflow Lite plan command for planning drafts, repo-aware plans, and external-agent handoffs.
argument-hint: "Request=<planning request>; Lens=<none|strategy|conceptual|test|architecture|iteration|expand|language>; Output=<compact|full>"
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
- Planning drafts are allowed; do not force build-ready detail unless the user asks for handoff or `Output: full`.
- Choose `Abstraction Level: phase-plan` or `implementation-plan` automatically unless the user explicitly names one.
- Inherit a source shape artifact's `Recommended Next Abstraction Level` when present; otherwise infer from the request and default to `phase-plan` when uncertain.
- If `implementation-plan` is requested but readiness is incomplete, downgrade to `phase-plan` and list `What Would Make This Implementation-Ready`.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
- Current Read, optional
- Abstraction Level
- Planning Basis
- Take
- Planning Draft: yes/no
- What Would Make This Implementation-Ready, when relevant
- Risks/Unknowns
- Next
- Persist Candidate, candidate only and do not write
