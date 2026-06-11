---
description: Workflow Lite stable-document sync command for project docs or session archive summaries.
argument-hint: "Sync Domain=<project-docs|session-archive>; Scope=<area/thread>; Target=<docs path, src README, or archive summary>; Source Of Truth=<source>; Request=<sync request>"
---

# wf-sync

Use Workflow Lite sync semantics.

Mode: persist
Output: full
Task: sync
Lens: ${input:lens:none}
Scope: ${input:scope:required when target is missing}
Sync Domain: ${input:sync_domain:project-docs|session-archive}
Target: ${input:target:optional docs target, src/**/README.md, or .session/archive/<thread>/summary.md}
Source Of Truth: ${input:source_of_truth:code|diff|session thread artifact|explicit user decision|existing docs}

Rules:
- Write only stable-document targets for the selected domain: allowed project docs targets, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.
- Allowed Project Doc Types: `docs/architecture/**`, `docs/design/**`, `docs/adr/**`, `docs/operations/**`, `docs/reference/**`, and `src/**/README.md`.
- For `session-archive`, require Source Thread, Thread Status, Archive Purpose, Summary Scope, Next Retrieval Use, and target `.session/archive/<thread>/summary.md`.
- Do not create workflow-internal docs such as `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares host-project taxonomy override.
- Do not write `.session/threads/**`, `.session/inbox/**`, `notes/**`, source code, `.workflow/**`, or `.github/**`.
- If both `Target` and `Scope` are missing, output `docs blocked` or `archive blocked`.
- If source of truth is unclear, route to `wf-review` with `Lens: consistency`.
- Use `.workflow/tasks/sync.md` as the task contract.

Request:
${input:request:describe the stable-document sync to perform}

Return:
- Sync Domain
- Scope
- Docs Type Gate
- Archive Criteria, when relevant
- Alignment Set
- Target
- Changes made or `docs blocked` / `archive blocked`
- Blocked Items
- Follow-up Review Needed
