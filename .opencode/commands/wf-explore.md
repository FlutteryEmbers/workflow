---
description: Workflow Lite read-only evidence extraction.
agent: workflow-context
---

Use Workflow Lite explore semantics.

Mode: discuss
Output: compact
Task: explore
Lens: none unless explicitly requested; allowed: architecture, debug, language

Rules:
- Do not edit files.
- Extract evidence only; do not give a final verdict or choose the final direction.
- You may include candidate interpretations, likely entrypoints, and recommended next task, but keep them evidence-grounded and non-authoritative.
- Route user-directed summary or distillation requests to `/wf-distill`.
- Do not default to the `consistency` lens for discovery questions.
- Treat external or unfamiliar repo conflicts as reliability risks, not repair tasks.
- Use `.workflow/tasks/explore.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- User Intent
- Current Read, optional
- Take, 3-5 bullets max
- Risks/Unknowns, 0-3 bullets
- Candidate Interpretations, optional
- Recommended Next Task
- Next
- Persist Candidate, candidate only and do not write
