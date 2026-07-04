---
description: OpenCode read-only context summary for low-model use.
agent: workflow-context
---

Use OpenCode as a read-only context helper.

Rules:
- Do not edit files.
- Do not create an implementation plan.
- Do not route Workflow Lite tasks.
- Do not use lenses, templates, session writes, or docs sync.
- Report observed facts and concrete paths only.

Request:
$ARGUMENTS

Return:
- Current state
- Relevant files
- Missing evidence
- Unknowns
- Next action packet:

```text
Goal:
Source Context:
Locked Decisions:
Open Questions:
Allowed Changes:
Do Not Touch:
Verification:
Next Action:
```
