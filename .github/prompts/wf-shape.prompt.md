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

Request:
${input:request:describe the what-if, strategy, concept, or direction-setting question}

Return:
- Understanding
- Take
- Risks/Unknowns
- Provisional Recommendation
- Human Decision Needed
- Persist Hint
