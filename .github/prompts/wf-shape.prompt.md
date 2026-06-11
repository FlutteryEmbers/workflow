---
description: Workflow Lite shape command for what-if, strategy, conceptual, and direction-setting discussion.
argument-hint: "Request=<direction question>; Lens=<none|strategy|conceptual|domain|architecture|iteration|expand|distill|language|redteam>"
---

# wf-shape

Use Workflow Lite shape semantics.

Mode: discuss
Output: compact
Task: shape
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/shape.md` as the task contract.
- Provide provisional thinking when useful; do not present it as approval, readiness, or execution permission.
- Output `Abstraction Level: concept` for conceptual shape work.
- Include `Impact Surface` and `Recommended Next Abstraction Level` when this shape may feed planning.

Request:
${input:request:describe the what-if, strategy, concept, or direction-setting question}

Return:
- User Intent
- Current Read, optional
- Abstraction Level, when concept structure is relevant
- Take
- Risks/Unknowns
- Provisional Recommendation
- Impact Surface, when planning may follow
- Human Decision Needed
- Persist Candidate, candidate only and do not write
