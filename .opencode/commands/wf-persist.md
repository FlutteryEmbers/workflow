---
description: Persist Workflow Lite session artifact.
---

Use Workflow Lite persistence semantics.

Mode: persist
Task: persist
Output: full
Lens: none unless explicitly requested; allowed: language

Rules:
- Write only `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**`.
- Use `Brief Type: external-goal` only for long, external, or reusable goal material stored as an inbox brief before shape.
- Infer thread targets by same-work-item fit when `Thread` is absent; recency alone is not enough.
- Include `Thread Inference Note` when target selection depends on assumptions or low-confidence fit.
- Do not write `docs/**`, source code, `.workflow/**`, or `.github/**`.
- Use `.workflow/tasks/persist.md` and the matching `.workflow/templates/<artifact>.md` as contracts if needed.
- You may consume a `Persist Candidate`, recent discussion, explicit source files, or user-provided content.
- Preserve decision-relevant reasoning; do not save full transcript by default.

Request:
$ARGUMENTS

Return:
- Target written or proposed
- Thread Inference Note
- Artifact kind
- Thread/topic
- Any missing prerequisite
