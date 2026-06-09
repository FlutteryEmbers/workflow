---
description: Workflow Lite clarify command for terms, prior answers, statements, and request reframing.
argument-hint: "Request=<term, statement, prior answer, or unclear request>; Lens=<none|language|domain|iteration>"
---

# wf-clarify

Use Workflow Lite clarify semantics.

Mode: discuss
Output: compact
Task: clarify
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/clarify.md` as the task contract.
- Explain or restate meaning; do not judge correctness, reasonableness, readiness, or conflicts.
- Always include exactly one short `Example`.
- If the user asks whether something is correct, reasonable, conflicting, safe, or ready, route to `/wf-review`.

Request:
${input:request:term, prior AI answer, statement, workflow rule, or unclear request to clarify}

Return:
- User Intent
- Term / Statement
- Plain Meaning
- In This Workflow
- Common Confusion
- Example
- Next
- Persist Candidate, candidate only and do not write
