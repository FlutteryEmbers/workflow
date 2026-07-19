---
description: Workflow Lite persist command for writing session artifacts or explicit disposable notes.
argument-hint: "Artifact=<kind>; Artifact State=<inbox|working|settled|superseded>; Thread=<thread>; Topic=<topic>; Target=<optional>; Intent=<intent>; Depth=<depth>; Lens=<none|language>; Request=<source or changes>"
---

# wf-persist

Use Workflow Lite persist semantics.

Mode: persist
Task: persist
Lens: ${input:lens:none}
Artifact: ${input:artifact:brief|note|shape|plan|review|distillation}
Brief Type: ${input:brief_type:general|external-goal; only for Artifact=brief}
Artifact State: ${input:artifact_state:inbox|working|settled|superseded}
Thread: ${input:thread:optional thread}
Topic: ${input:topic:file-safe topic}
Target: ${input:target:optional explicit target}
Intent: ${input:intent:summary|exploration|decision|audit|handoff|constraint|reference|capture}
Depth: ${input:depth:compact|standard|detailed}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
- Write only `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**`.
- Load `.workflow/lenses/language.md` only when the user explicitly selects `language`.
- Use `Brief Type: external-goal` only for long, external, or reusable goal material stored as an inbox brief before shape.
- Use `Artifact=note`, `Artifact State=inbox`, and `Intent=capture` for untriaged knowledge captures such as reusable build execution discoveries.
- Inbox capture is not source of truth and is not an execution source; later `review`, `plan`, or `sync` must promote stable conclusions.
- Infer thread targets by same-work-item fit when `Thread` is absent; recency alone is not enough.
- Include `Thread Inference Note` when target selection depends on assumptions or low-confidence fit.
- Do not write `docs/**`, source code, `.workflow/**`, or `.github/**`.
- Load `.workflow/tasks/persist.md`, the matching artifact template, and `.workflow/templates/_persist_metadata.md`.
- Resolve source in this order: explicit source/path, Artifact ID, Persist Candidate, same-work-item artifacts, then matching recent discussion and user corrections.
- Write every required template section at every Depth; use `unknown` or `none` when source content cannot be derived. Do not write a partial artifact when prerequisites are missing or an interactive checkpoint is unresolved.
- Preserve decision-relevant reasoning, not full transcript.
- If target belongs to another write boundary, route to `wf-sync`, external-agent, or manual workflow-lite fallback.

Request:
${input:request:describe source discussion, Persist Candidate, existing artifact, or requested edit}

Return:
- `User Intent`: persistence request.
- `Task State`: result, target, and blocking reason when any.
- `Primary Result`: short write receipt; omit when blocked.
- `Supporting Information`: template, source basis, Depth, and Thread Inference Note when relevant.
- `Next`: artifact use, missing prerequisite, or none.
