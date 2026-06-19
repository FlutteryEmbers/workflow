---
description: Workflow Lite evidence mapping and non-mutating probes.
agent: workflow-context
---

Use Workflow Lite explore semantics.

Mode: discuss
Output: compact
Task: explore
Lens: none unless explicitly requested; allowed: architecture, boundary, debug, language

Rules:
- Do not edit files.
- Output Shape: Evidence Map.
- Use evidence-shaped fields: observed facts, evidence map, probes, reliability, missing evidence, evidence sufficiency, downstream use, follow-up targets, candidate review targets.
- Treat explore as upstream evidence for shape and review. Recommend `plan` only when direction or target is already selected and evidence only fills repo-aware planning context.
- If the request asks for judgment, answer with evidence plus `Candidate Review Targets`; do not output verdict fields.
- You may run non-mutating probes, dry-runs, list/collect commands, existing read-only repo commands, one-off shell probes, or temporary scripts outside the repo to establish evidence.
- Do not create or modify repo scripts, tests, fixtures, configs, generated files, docs, or tracked files.
- Every probe must include `Probe`, `Command or Method`, `Observed Result`, `Reliability`, and `Side Effect Check`.
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
- Evidence Probes, when used
- Missing Evidence
- Evidence Sufficiency
- Downstream Use
- Candidate Interpretations, optional
- Follow-up Targets
- Candidate Review Targets, optional
- Recommended Next Task
- Next
- Persist Candidate, candidate only and do not write
