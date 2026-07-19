---
description: Workflow Lite clarify command for terms, prior answers, statements, and request reframing.
argument-hint: "Request=<term, statement, prior answer, or unclear request>; Lens=<none|language>"
---

# wf-clarify

Use Workflow Lite clarify semantics.

Mode: discuss
Task: clarify
Lens: ${input:lens:none}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/clarify.md` as the task contract.
- Explain or restate meaning; do not judge correctness, reasonableness, readiness, or conflicts.
- You may include a lightweight next-task hint, but do not perform evidence audit, summary, verdict, planning, write, sync, or execution.
- Always include exactly one short `Example`.
- If the user asks whether something is correct, reasonable, conflicting, safe, or ready, route to `/wf-review`.

Request:
${input:request:term, prior AI answer, statement, workflow rule, or unclear request to clarify}

Return:
- `User Intent`: clarification request.
- `Task State`: Boundary Advice and Term / Statement when relevant.
- `Primary Result`: Plain Meaning and In This Workflow.
- `Supporting Information`: Why It Matters, Common Confusion, Example, related concepts, assumptions, and open questions when relevant.
- `Next`: Recommended Next Task and use of the clarification.
- `Persistence`: Persist Candidate only when worth saving; candidate only and do not write.
