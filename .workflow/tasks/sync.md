---
id: sync
role: steward
purpose: Project confirmed outcomes into stable documents: project docs / code-adjacent README, or session archive summaries.
inputs:
  - sync_domain
  - source
  - target
  - scope
  - source_of_truth
  - alignment_success_criteria
  - source_thread
  - thread_status
  - archive_purpose
  - summary_scope
  - next_retrieval_use
outputs:
  - docs/architecture/**/*
  - docs/design/**/*
  - docs/adr/**/*
  - docs/operations/**/*
  - docs/reference/**/*
  - src/**/README.md
  - .session/archive/**/summary.md
user_selectable_lenses:
  - consistency
  - distill
  - language
  - domain
  - architecture
done_check:
  - sync_domain_is_named
  - source_is_confirmed
  - target_boundary_is_valid
  - stable_document_rules_are_satisfied
---

# Sync Task

`sync` is the stable-document projection task. It writes only:

- `Sync Domain: project-docs` -> `docs/**` or explicit `src/**/README.md`
- `Sync Domain: session-archive` -> `.session/archive/<thread>/summary.md`

`sync` does not maintain active session working memory. `.session/threads/**`, `.session/inbox/**`, `.session/goal/**`, and explicit `notes/**` remain `persist` targets.

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; state the stable-document projection the user wants.
- `Mode: discuss` is default: recommend sync domain, targets, prerequisites, and blockers in chat; do not load templates and do not write files.
- `Mode: persist`: write only allowed targets for the selected sync domain.
- `Mode: execute`: not valid for this task.

Allowed write targets in `Mode: persist`:

- `project-docs`: `docs/**` or explicit `src/**/README.md`
- `session-archive`: `.session/archive/<thread>/summary.md`

Rejected targets:

- `.session/threads/**`, `.session/inbox/**`, `.session/goal/**`: use `persist`.
- `notes/**`: use `persist` with an explicit notes target.
- source code, `.workflow/**`, `.github/**`, prompts, templates, and other repository artifacts: use `plan -> build` or the external-agent path.

## When To Use

- Use `project-docs` when reviewed/planned decisions, code, diffs, tests, or existing docs cleanup need stable projection into project docs or a code-adjacent README.
- Use `session-archive` when a completed thread needs a stable archive summary under `.session/archive/<thread>/summary.md`.
- Use after `review --lens consistency -> plan --lens consistency` when docs/code, docs/docs, or thread/docs drift has been confirmed.
- Use after `review -> shape -> plan` when source of truth or document architecture was unclear and has since been settled.

## Do Not Use When

- Do not decide whether code, docs, a thread, or a plan is correct; use `review`.
- Do not decide source of truth or redesign docs/thread ownership; use `shape`.
- Do not sequence repairs across files; use `plan`.
- Do not maintain active thread metadata, move/rename thread files, or repair `.session/threads/**`; use `persist` or an explicit plan for repository changes.
- Do not change source code or workflow system files; use `build` or the external-agent path.

## Expected Output

- `Mode: discuss`: sync domain, target or alignment/archive set, prerequisites, blockers, and next prompt.
- `Mode: persist`: write only the selected stable document target(s), with blocked items when prerequisites are missing.

## Task Boundary Check

Before syncing, classify the request:

- `fits`: request targets `docs/**`, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md` with the required source and domain prerequisites.
- `fits_with_preflight`: sync depends on checking source, existing docs/archive, code, diff, thread artifacts, consistency, alignment criteria, or archive prerequisites. In `Mode: discuss`, run default implicit preflight first.
- `wrong_task`: request asks to judge correctness, source of truth, readiness, or conflict; recommend `review --lens consistency`.
- `wrong_task`: request asks to decide docs architecture, artifact ownership, or long-term boundary; recommend `shape`.
- `wrong_task`: request asks to sequence repairs or create a multi-target work plan; recommend `plan`.
- `wrong_task`: request targets `.session/threads/**`, `.session/inbox/**`, `.session/goal/**`, or `notes/**`; recommend `persist`.
- `wrong_task`: request targets code, `.workflow/**`, `.github/**`, prompts, templates, or unrelated files; recommend `plan -> build` or external-agent.
- `missing_prerequisite`: `Sync Domain` is missing and cannot be inferred from target.
- `missing_prerequisite`: `Target` and `Scope` are both missing.
- `missing_prerequisite`: `project-docs` lacks source, scope, docs type, source of truth, alignment success criteria, or Project Docs Rules safety.
- `missing_prerequisite`: `session-archive` lacks source thread, thread status, archive purpose, summary scope, next retrieval use, or archive target.

Default implicit preflight runs only in `Mode: discuss`. In `Mode: persist`, do not preflight; block with `docs blocked` or `archive blocked` when prerequisites are missing.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Sync Domains

### project-docs

Purpose: project confirmed source of truth into code-aligned project docs or code-adjacent README files.

Allowed targets:

- `architecture`: `docs/architecture/**`
- `design`: `docs/design/**`
- `adr`: `docs/adr/**`
- `operations`: `docs/operations/**`
- `reference`: `docs/reference/**`
- `code-readme`: `src/**/README.md`

Required:

- Source
- Scope
- Docs type
- Target or alignment set
- Source of truth
- Alignment success criteria
- Project Docs Rules safety

### session-archive

Purpose: project a completed thread into a stable archive summary.

Allowed target:

- `.session/archive/<thread>/summary.md`

Required:

- Source Thread: `.session/threads/<thread>/**`
- Thread Status: `settled | superseded | abandoned | implemented | blocked`
- Archive Purpose
- Summary Scope
- Next Retrieval Use
- Target: `.session/archive/<thread>/summary.md`

`session-archive` must not move, rename, delete, or edit active thread files. Batch archive generation requires an explicit plan; single-thread archive sync is the default.

## Templates

- Project-docs sync: `.workflow/templates/sync.md`
- New project doc target: `.workflow/templates/project_doc.md`
- New architecture note target: `.workflow/templates/architecture_note.md`
- Code-adjacent README targets: `.workflow/templates/code_readme.md`
- Session archive summary: `.workflow/templates/archive_summary.md`

## Copilot Add Context

Required:

- #.workflow/tasks/sync.md
- source artifact(s): reviewed plan, review verdict, explicit user decision, existing docs, source code/diff/tests, or `.session/threads/<thread>/**`
- target stable document or scope

For `Mode: persist`:

- Add the matching template for the selected sync domain.
- Provide `Sync Domain`.
- Provide either `Target` or `Scope`.
- For `session-archive`, provide `Source Thread`, `Thread Status`, `Archive Purpose`, `Summary Scope`, `Next Retrieval Use`, and `Target`.

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Project Docs Rules

Any write to `docs/**` must:

- Name source, scope, docs type, and source of truth.
- Name alignment success criteria for the target document.
- Preserve settled, confirmed facts useful for future code/docs alignment.
- Preserve existing docs structure and tone when updating an existing file.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, temporary PoC details, low-level implementation mirror content, and details that would mislead future execution.
- Output `docs blocked` and do not write `docs/**` when source, scope, docs type, source of truth, alignment success criteria, or safety is unclear.

Other `docs/**` paths are blocked unless the user explicitly provides the target and states it is part of the host project's existing docs taxonomy.

Do not create workflow-internal project docs by default. Block these targets unless the user explicitly provides the target and states it is host-project taxonomy:

- `docs/workflow/**`
- `docs/session/**`
- `docs/ai/**`
- `docs/prompts/**`
- `docs/notes/**`
- `docs/plans/**`
- `docs/reviews/**`

Do not sync `.workflow/**`, task/lens/template/prompt usage, session operation mechanics, AI workflow instructions, or Workflow Lite internal rules into `docs/**`. Persist that material to `.session/**` if it is session memory, or keep it in `.workflow/**` if it is workflow system guidance.

## Archive Rules

Any write to `.session/archive/**` must:

- Summarize only the named source thread.
- Preserve decision-relevant conclusions, key decisions, plans/execution state, reviews, docs synced, open questions, superseded/rejected directions, and retrieval notes.
- Mark unresolved conflicts or unclear source of truth instead of resolving them.
- Exclude full transcripts and low-value chat noise.
- Avoid presenting unconfirmed discussion as project fact.
- Output `archive blocked` and do not write `.session/archive/**` when source thread, thread status, archive purpose, summary scope, next retrieval use, or target is unclear.

## Instructions

`review` makes judgments. `shape` decides unclear source-of-truth or artifact-boundary questions. `plan` sequences repair work. `sync` performs stable-document projection only.

For `project-docs`, if `Target` exists, update only that target. If `Scope` exists without a target, inspect related docs/code and output an `Alignment Set` of at most 3 target candidates. If more than 3 targets are needed, split the work into batches and do not write them all at once.

For `session-archive`, default to one source thread and one archive summary target. If more than one thread must be archived, require an explicit plan that names the batch and stop conditions.

## Output Rules

- Output `Sync Domain: project-docs | session-archive`.
- Stable project docs go only to allowed project docs targets unless an explicit taxonomy override is provided.
- Code-adjacent README files go to `src/**/README.md`.
- Session archive summaries go only to `.session/archive/<thread>/summary.md`.
- Do not write `.session/threads/**`, `.session/inbox/**`, `.session/goal/**`, or `notes/**`.
- Do not create workflow-internal docs under `docs/**` by default.
- For scope-based project-docs sync, output `Alignment Set` before writing. Each item must include docs type, target, source, source of truth, and alignment reason.
- Default project-docs alignment set size is 1-3 targets. More than 3 targets must be split into batches.
- Include `Blocked Items` for unsupported docs types, workflow-internal targets, unclear source of truth, missing alignment criteria, missing archive prerequisites, or unsupported targets.
- Include `Follow-up Review Needed` when source-of-truth judgment is required.
- Project docs source from session must be explicit, usually `.session/threads/**`; inbox notes and exploration notes require explicit source-of-truth confirmation.
- With `consistency`, sync only confirmed outcomes. If code may be wrong, route to `plan -> build` or external-agent implementation; if intent is unclear, route to `shape`; if archive sources conflict, mark unresolved items or route to `review --lens consistency`.
- With `architecture`, project constraints go to `docs/architecture/boundaries.md` or a user-specified docs target.
- With `language`, settled terminology goes to `docs/reference/glossary.md` or a user-specified allowed docs target.
- With `distill`, reference-derived structures go to project docs after they are confirmed as project knowledge.

## User Input

{{sync domain, project doc update, code-adjacent README request, archive request, source thread, thread status, source of truth, target, scope, and alignment/archive criteria}}
