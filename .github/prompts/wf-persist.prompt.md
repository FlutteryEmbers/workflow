---
description: Workflow Lite persist command for writing session artifacts or explicit disposable notes.
argument-hint: "Artifact=<kind>; Thread=<thread>; Topic=<topic>; Target=<optional>; Intent=<intent>; Depth=<depth>; Request=<source or changes>"
---

# wf-persist

Use Workflow Lite persist semantics.

Mode: persist
Output: full
Task: persist
Lens: ${input:lens:none}
Artifact: ${input:artifact:brief|note|shape|option|plan|review|decision|distillation|expanded|goal}
Thread: ${input:thread:optional thread}
Topic: ${input:topic:file-safe topic}
Target: ${input:target:optional explicit target}
Intent: ${input:intent:summary|exploration|decision|audit|handoff|constraint|reference}
Depth: ${input:depth:compact|standard|detailed}

Rules:
- Write only `.session/**` or explicit `notes/**`.
- Do not write `docs/**`, source code, `.workflow/**`, or `.github/**`.
- Load `.workflow/tasks/persist.md` and the matching artifact template.
- Preserve decision-relevant reasoning, not full transcript.
- If target belongs to another write boundary, route to `wf-sync`, external-agent, or manual workflow-lite fallback.

Request:
${input:request:describe source discussion, Persist Hint, Persist Packet, existing artifact, or requested edit}

Return:
- Target
- Artifact metadata
- Written artifact or blocking reason
- Naming note, if explicit target differs from recommended naming
