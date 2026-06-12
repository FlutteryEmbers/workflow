---
description: Workflow Lite stable-object sync command for project docs or session archive summaries.
argument-hint: "Sync Domain=<project-docs|session-archive>; Sync Object=<architecture|feature|reference|code-readme|archive-summary|all>; Scope=<area/thread>; Target Directory=<optional>; Target=<docs path, src README, or archive summary>; Source Of Truth=<source>; Request=<sync request>"
---

# wf-sync

Use Workflow Lite sync semantics.

Mode: persist
Output: full
Task: sync
Lens: ${input:lens:none}
Scope: ${input:scope:required when target is missing}
Sync Domain: ${input:sync_domain:project-docs|session-archive}
Sync Object: ${input:sync_object:architecture|feature|reference|code-readme|archive-summary|all}
Target Directory: ${input:target_directory:optional docs directory, src area, or archive directory}
Target: ${input:target:optional docs target, src/**/README.md, or .session/archive/<thread>/summary.md}
Source Of Truth: ${input:source_of_truth:code|diff|session thread artifact|explicit user decision|existing docs}

Rules:
- Write only stable-document targets for the selected domain: `docs/**`, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.
- Use `Sync Object` to select the stable object: `architecture`, `feature`, `reference`, `code-readme`, `archive-summary`, or `all`.
- `Target` wins; `Target Directory` may guide creation/update; otherwise use an existing docs convention or output an Alignment Set.
- For `session-archive`, require Source Thread, Thread Status, Archive Purpose, Summary Scope, Next Retrieval Use, and target `.session/archive/<thread>/summary.md`.
- Do not create workflow-internal docs such as `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares host-project taxonomy override.
- Do not write `.session/threads/**`, `.session/inbox/**`, `notes/**`, source code, `.workflow/**`, or `.github/**`.
- If `Sync Object`, source of truth, alignment criteria, and safe target selection are unclear, output `docs blocked` or `archive blocked`.
- If source of truth is unclear, route to `wf-review` with `Lens: consistency`.
- Use `.workflow/tasks/sync.md` as the task contract.

Request:
${input:request:describe the stable-document sync to perform}

Return:
- Sync Domain
- Sync Object
- Scope
- Sync Object Gate
- Archive Criteria, when relevant
- Alignment Set
- Target Directory
- Target
- Changes made or `docs blocked` / `archive blocked`
- Blocked Items
- Follow-up Review Needed
