---
description: Sync Workflow Lite stable documents: project docs, code-adjacent README, or session archive summary.
agent: workflow-docs-sync
---

Use Workflow Lite sync semantics.

Mode: persist
Task: sync
Output: full
Lens: none unless explicitly requested; allowed: consistency, architecture, distill, language

Rules:
- Write only stable-document targets for the selected domain: `docs/**`, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.
- Do not write `.session/threads/**`, `.session/inbox/**`, `notes/**`, source code, `.workflow/**`, or `.github/**`.
- Require source, scope, sync object, source of truth, alignment success criteria, and safe target selection for `docs/**`.
- For `session-archive`, require Source Thread, Thread Status, Archive Purpose, Summary Scope, Next Retrieval Use, and target `.session/archive/<thread>/summary.md`.
- Use `Sync Object: architecture | feature | reference | code-readme | archive-summary | all`.
- `Target` wins; `Target Directory` may guide creation/update; otherwise use an existing docs convention or output an Alignment Set.
- Do not create workflow-internal docs such as `docs/workflow/**`, `docs/session/**`, `docs/ai/**`, `docs/prompts/**`, `docs/notes/**`, `docs/plans/**`, or `docs/reviews/**` unless the user explicitly declares host-project taxonomy override.
- If `Target` is missing, require `Scope` and return an `Alignment Set` of at most 3 targets before writing.
- Use `.workflow/tasks/sync.md` as the task contract if needed.
- If Project Docs Rules are not satisfied, output `docs blocked`; if Archive Rules are not satisfied, output `archive blocked`.

Request:
$ARGUMENTS

Return:
- Sync Domain
- Sync Object
- Scope
- Sync Object Gate
- Archive Criteria, when relevant
- Alignment Set
- Target Directory
- Target
- Source of truth
- Changes made or `docs blocked` / `archive blocked`
- Blocked Items
- Follow-up
