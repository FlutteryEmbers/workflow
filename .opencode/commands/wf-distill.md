---
description: Distill Workflow Lite source material into a user-directed summary.
---

Use Workflow Lite distill semantics.

Mode: discuss
Output: compact
Task: distill
Lens: none unless explicitly requested; allowed: language

Rules:
- Do not edit files.
- Summarize only the requested source and focus.
- Separate `Observed`, `Inferred`, and `Unknown`.
- Do not judge accuracy, source of truth, or readiness; route those to `review`.
- Do not write `.session/**`, `docs/**`, or `.session/archive/**`.
- Use `.workflow/tasks/distill.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- User Intent
- Source Scope
- Summary Focus
- Summary Type
- Summary
- Observed
- Inferred
- Unknown
- Next Use
- Persist Candidate, candidate only and do not write
