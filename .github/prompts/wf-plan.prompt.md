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

Request:
${input:request:describe the chosen direction and planning need}

Return:
- User Intent
- Current Read, optional
- Take
- Planning Draft: yes/no
- Risks/Unknowns
- Next
- Persist Candidate, candidate only and do not write
