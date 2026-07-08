---
description: Workflow Lite explore command for descriptive inquiry, observed system mapping, and non-mutating probes.
argument-hint: "Request=<code, docs, behavior, entrypoint, dependency, or reference to explore>; Lens=<none|architecture|boundary|debug|language>"
---

# wf-explore

Use Workflow Lite explore semantics.

Mode: discuss
Output: compact
Task: explore
Lens: ${input:lens:none}

Rules:
- Do not write files.
- Do not load templates.
- Load selected lenses only when explicitly named.
- Use `.workflow/tasks/explore.md` as the task contract.
- Output Shape: Observed System Map.
- Start non-trivial output with `Explore Frame`, then answer with `Observed Answer`, then provide `Evidence Basis`.
- Use descriptive inquiry fields: inquiry type, source scope, how it works, what exists, differences observed, entrypoints/flow, not found in checked scope, not checked, probes, reliability/not checked, evidence sufficiency, downstream use, follow-up targets, candidate review targets.
- Treat explore as descriptive inquiry for shape and review. Recommend `plan` only when direction or target is already selected and evidence only fills repo-aware planning context.
- If the request asks for judgment, answer with evidence plus `Candidate Review Targets`; do not output verdict fields.
- You may run non-mutating probes, dry-runs, list/collect commands, existing read-only repo commands, one-off shell probes, or temporary scripts outside the repo to establish evidence.
- Do not create or modify repo scripts, tests, fixtures, configs, generated files, docs, or tracked files.
- Every probe must include `Probe`, `Command or Method`, `Observed Result`, `Reliability`, and `Side Effect Check`.
- You may include candidate interpretations, likely entrypoints, and recommended next task, but keep them evidence-grounded and non-authoritative.
- Difference questions should output `Differences Observed`, not a conflict/source-of-truth verdict. "Not found" must name the checked scope.
- Route user-directed summary or distillation requests to `/wf-distill`.
- Do not default to the `consistency` lens for discovery questions.
- Treat external or unfamiliar repo conflicts as reliability risks, not repair tasks.

Request:
${input:request:describe the code, docs, behavior, entrypoint, dependency, or reference to explore}

Return:
- User Intent
- Explore Frame
- Observed Answer
- Evidence Basis
- Reliability / Not Checked
- Evidence Probes, when used
- Evidence Sufficiency
- Downstream Use
- Candidate Interpretations, optional
- Follow-up Targets
- Candidate Review Targets, optional
- Recommended Next Task
- Persist Candidate, candidate only and do not write
