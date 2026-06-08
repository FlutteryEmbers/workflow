---
description: Workflow Lite sync command for docs/code alignment stage.
argument-hint: "Scope=<area>; Target=<allowed docs path or src README>; Source Of Truth=<source>; Request=<alignment request>"
---

# wf-sync

Use Workflow Lite sync semantics.

Mode: persist
Output: full
Task: sync
Lens: ${input:lens:none}
Scope: ${input:scope:required when target is missing}
Target: ${input:target:optional allowed docs target or src/**/README.md}
Source Of Truth: ${input:source_of_truth:code|diff|session thread artifact|explicit user decision|existing docs}

Rules:
- Write only allowed project docs targets or explicit `src/**/README.md`.
- Allowed Project Doc Types: `docs/architecture/**`, `docs/design/**`, `docs/adr/**`, `docs/operations/**`, `docs/reference/**`, and `src/**/README.md`.
- Do not create workflow-internal docs such as `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares host-project taxonomy override.
- If both `Target` and `Scope` are missing, output `docs blocked`.
- If source of truth is unclear, route to `wf-review` with `Lens: consistency`.
- Use `.workflow/tasks/sync.md` as the task contract.

Request:
${input:request:describe the docs/code alignment to perform}

Return:
- Scope
- Docs Type Gate
- Alignment Set
- Target
- Changes made or `docs blocked`
- Blocked Items
- Follow-up Review Needed
