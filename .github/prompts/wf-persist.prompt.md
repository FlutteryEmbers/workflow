---
description: Workflow Lite persist command for writing session artifacts or explicit disposable notes.
argument-hint: "Artifact=<kind>; Artifact State=<inbox|working|settled|superseded>; Thread=<thread>; Topic=<topic>; Target=<optional>; Intent=<intent>; Depth=<depth>; Request=<source or changes>"
---

# wf-persist

Use Workflow Lite persist semantics.

Mode: persist
Output: full
Task: persist
Lens: ${input:lens:none}
Artifact: ${input:artifact:brief|note|shape|option|plan|review|decision|distillation|expanded}
Brief Type: ${input:brief_type:general|external-goal; only for Artifact=brief}
Artifact State: ${input:artifact_state:inbox|working|settled|superseded}
Thread: ${input:thread:optional thread}
Topic: ${input:topic:file-safe topic}
Target: ${input:target:optional explicit target}
Intent: ${input:intent:summary|exploration|decision|audit|handoff|constraint|reference|capture}
Depth: ${input:depth:compact|standard|detailed}

Rules:
- Write only `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**`.
- Use `Brief Type: external-goal` only for long, external, or reusable goal material stored as an inbox brief before shape.
- Use `Artifact=note`, `Artifact State=inbox`, and `Intent=capture` for untriaged knowledge captures such as reusable build execution discoveries.
- Inbox capture is not source of truth and is not an execution source; later `review`, `plan`, or `sync` must promote stable conclusions.
- Infer thread targets by same-work-item fit when `Thread` is absent; recency alone is not enough.
- Include `Thread Inference Note` when target selection depends on assumptions or low-confidence fit.
- Do not write `docs/**`, source code, `.workflow/**`, or `.github/**`.
- Load `.workflow/tasks/persist.md` and the matching artifact template.
- Preserve decision-relevant reasoning, not full transcript.
- If target belongs to another write boundary, route to `wf-sync`, external-agent, or manual workflow-lite fallback.

Request:
${input:request:describe source discussion, Persist Candidate, Persist Packet, existing artifact, or requested edit}

Return:
- Target
- Thread Inference Note
- Artifact metadata
- Written artifact or blocking reason
- Naming note, if explicit target differs from recommended naming
