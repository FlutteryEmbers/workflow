---
description: OpenCode read-only plan or diff review for low-model use.
agent: workflow-review
---

Review the supplied plan or diff. Do not edit files.

Rules:
- Start with the review verdict.
- Compare a diff against the explicit plan before giving broader comments.
- Report blocking gaps separately from non-blocking gaps.
- Do not rewrite the full solution.
- Do not route Workflow Lite tasks.
- Do not use lenses, templates, session writes, or docs sync.

Request:
$ARGUMENTS

Return:
```text
Review Verdict: ready | needs changes | needs more evidence | blocked
```

- Evidence checked
- Blocking gaps
- Non-blocking gaps
- Recommended next action
