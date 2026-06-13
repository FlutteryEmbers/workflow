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
- Use `Artifact=note`, `Artifact State=inbox`, and `Intent=capture` for untriaged knowledge captures such as reusable build execution discoveries.
- Inbox capture is not source of truth and is not an execution source; later `review`, `plan`, or `sync` must promote stable conclusions.
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
