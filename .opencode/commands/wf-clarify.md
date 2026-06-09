---
description: Workflow Lite term, statement, prior-answer, and request clarification.
---

Use Workflow Lite clarify semantics.

Mode: discuss
Output: compact
Task: clarify
Lens: none unless explicitly requested

Rules:
- Do not edit files.
- Do not load templates.
- Clarify terms, prior AI answers, statements, goals, scope, constraints, and success criteria.
- Do not judge correctness, reasonableness, readiness, or conflicts; route those to review.
- Always include exactly one short `Example`.
- If domain facts are uncertain, use a generic illustrative example and do not introduce new domain claims.
- Use `.workflow/tasks/clarify.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- User Intent
- Term / Statement
- Plain Meaning
- In This Workflow
- Common Confusion
- Example
- Next
- Persist Candidate, candidate only and do not write
