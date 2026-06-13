---
description: Workflow Lite plan command for planning drafts, repo-aware plans, and external-agent handoffs.
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
- Planning drafts are allowed; do not force build-ready detail unless the user asks for handoff or `Output: full`.
- Compact plan output must still summarize shape/chosen direction and include a compact impact surface.
- Use `Shape Summary: Source=chat` when there is no persisted shape artifact.
- Use `Output: full` for persisted plans, implementation handoffs, build-ready plans, or external-agent handoffs.
- Choose `Abstraction Level: phase-plan` or `implementation-plan` automatically unless the user explicitly names one.
- Inherit a source shape artifact's `Recommended Next Abstraction Level` when present; otherwise infer from the request and default to `phase-plan` when uncertain.
- If `implementation-plan` is requested but readiness is incomplete, downgrade to `phase-plan` and list `What Would Make This Implementation-Ready`.
- Replace generic open questions with `Blocking Questions`; use `Follow-up Questions` only in full or refine output.

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
- Shape Summary
- Impact Surface
- Plan
- Blocking Questions
- Abstraction Level
- Planning Draft: yes/no
- What Would Make This Implementation-Ready, when relevant
- Next
- Persist Candidate, candidate only and do not write
