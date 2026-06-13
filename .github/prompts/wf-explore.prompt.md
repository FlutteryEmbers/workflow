---
description: Workflow Lite explore command for read-only evidence extraction.
argument-hint: "Request=<code, docs, behavior, entrypoint, dependency, or reference to explore>; Lens=<none|architecture|debug|distill|language>"
---

# wf-explore

Use Workflow Lite explore semantics.

Mode: discuss
Output: compact
Task: explore
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/explore.md` as the task contract.
- Extract evidence only; do not give a final verdict, choose the final direction, or create a plan.
- Do not default to the `consistency` lens for discovery questions.
- Treat external or unfamiliar repo conflicts as reliability risks, not repair tasks.

Request:
${input:request:describe the code, docs, behavior, entrypoint, dependency, or reference to explore}

Return:
- User Intent
- Current Read, optional
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Next
- Persist Candidate, candidate only and do not write
