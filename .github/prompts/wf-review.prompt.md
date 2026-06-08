---
description: Workflow Lite review command for verdicts, audits, diff reviews, and docs/code drift judgment.
argument-hint: "Request=<target to review>; Lens=<none|redteam|consistency|test|architecture|debug|language|domain>"
---

# wf-review

Use Workflow Lite review semantics.

Mode: discuss
Output: compact
Task: review
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/review.md` as the task contract.
- Give verdict and minimal repair direction when useful; do not create a full replacement design.

Request:
${input:request:describe the plan, diff, docs/code drift, claim, or artifact to review}

Return:
- Understanding
- Findings or Take
- Decision
- Confidence
- Minimal Revision Sketch
- Recommended Action
- Persist Hint
