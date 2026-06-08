---
id: sync
role: steward
purpose: Align confirmed source of truth into project docs and code-adjacent README files within an explicit scope.
inputs:
  - source_or_diff
  - scope
  - docs_type
  - source_of_truth
  - alignment_set
outputs:
  - docs/architecture/**/*
  - docs/design/**/*
  - docs/adr/**/*
  - docs/operations/**/*
  - docs/reference/**/*
  - src/**/README.md
user_selectable_lenses:
  - consistency
  - distill
  - language
  - domain
  - architecture
done_check:
  - source_of_truth_is_named
  - project_docs_rules_are_satisfied
  - stale_information_is_removed
---

# Sync Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must state what docs/code alignment the user wants.
- `Mode: discuss` is default: recommend sync targets in chat, do not load templates, and do not write files.
- `Mode: persist`: write only `docs/**` or explicit `src/**/README.md` targets.
- Reject `.session/**` targets; session files are maintained by `persist`.
- Reject `notes/**` targets; disposable exploration notes are maintained by `persist`.
- `Mode: execute`: not valid for this task.

## When To Use

- Use when confirmed decisions, code, diffs, tests, or existing docs cleanup need to be aligned into project docs or `src/**/README.md`.
- Use as the explicit docs/code alignment stage after review/build/external-agent work.
- Use when a known source of truth should update one target or a small alignment set within a named scope.

## Do Not Use When

- Do not use to maintain `.session/**`; use `persist`.
- Do not use to maintain `notes/**`; use `persist`.
- Do not use to decide whether code or docs are correct, acceptable, or source of truth; use `review`.
- Do not use to change source code; use `plan -> build` or the external-agent path.

## Expected Output

- `Mode: discuss`: proposed sync target or alignment set, docs type gate, source of truth, alignment success criteria, blockers, and next prompt.
- `Mode: persist`: updates only to `docs/**` or explicit `src/**/README.md` targets.

## Task Boundary Check

Before syncing, classify the request:

- `fits`: user asks to align code-aligned project docs or code-adjacent README with confirmed source.
- `fits_with_preflight`: sync depends on checking source, existing docs, code, diff, consistency, alignment success criteria, or Project Docs Rules. In `Mode: discuss`, run default implicit preflight first.
- `wrong_task`: user asks to persist any session artifact; recommend `persist`.
- `wrong_task`: user asks to change code; recommend `plan -> build` or external-agent.
- `missing_prerequisite`: both `Target` and `Scope` are missing.
- `missing_prerequisite`: `Mode: persist` target is missing or is not an allowed project docs target or `src/**/README.md`.
- `wrong_task`: target is `notes/**`; recommend `persist` because exploration notes are not project docs.
- `missing_prerequisite`: source, scope, docs type, source of truth, alignment success criteria, or Project Docs Rules safety is unclear for `docs/**`.
- `missing_prerequisite`: session source is not explicitly named or source of truth is unclear.

Default implicit preflight runs only in `Mode: discuss` and checks source, target or scope, docs type, source of truth, existing docs structure, and Project Docs Rules. In `Mode: persist`, do not preflight; block with `docs blocked` when any project docs prerequisite is missing.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Persist Templates

- Project docs sync: `.workflow/templates/sync.md`
- New project doc target: `.workflow/templates/project_doc.md`
- New architecture note target: `.workflow/templates/architecture_note.md`
- Code-adjacent README targets: `.workflow/templates/code_readme.md`

## Copilot Add Context

Required:

- #.workflow/tasks/sync.md
- explicit `.session/threads/**` artifact, existing `docs/**`, code, diff, tests, or code-adjacent README as needed
- `.session/inbox/**` or `notes/**` only when the user explicitly confirms source of truth

For `Mode: persist`:

- Add #.workflow/templates/sync.md for the sync operation.
- Add #.workflow/templates/project_doc.md or #.workflow/templates/architecture_note.md only when creating a new `docs/**` target.
- Add #.workflow/templates/code_readme.md for `src/**/README.md`.
- Provide either `Target` or `Scope`.
- Use allowed targets such as `docs/architecture/{topic}.md`, `docs/design/{topic}.md`, `docs/adr/{topic}.md`, `docs/operations/{topic}.md`, `docs/reference/{topic}.md`, or `src/{area}/README.md`.

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

## Allowed Project Doc Types

Default project docs targets are limited to:

- `architecture`: `docs/architecture/**`
- `design`: `docs/design/**`
- `adr`: `docs/adr/**`
- `operations`: `docs/operations/**`
- `reference`: `docs/reference/**`
- `code-readme`: `src/**/README.md`

Other `docs/**` paths are blocked unless the user explicitly provides the target and states it is part of the host project's existing docs taxonomy.

## No Workflow-Internal Docs Leakage

Do not create workflow-internal project docs by default. Block these targets unless the user explicitly provides the target and states it is host-project taxonomy:

- `docs/workflow/**`
- `docs/session/**`
- `docs/ai/**`
- `docs/prompts/**`
- `docs/notes/**`
- `docs/plans/**`
- `docs/reviews/**`

Do not sync `.workflow/**`, task/lens/template/prompt usage, session operation mechanics, AI workflow instructions, or Workflow Lite internal rules into `docs/**`. Persist that material to `.session/**` if it is session memory, or keep it in `.workflow/**` if it is workflow system guidance.

These Project Docs Rules are built-in safety, not the `redteam` lens. Do not run full critique during `sync`; recommend `review --lens redteam` only when project docs content exposes unresolved risk, sensitive assumptions, or high-cost commitments.

## Docs Type Gate And Create Docs Gate

Before creating or updating a target, classify `Docs Type`. If the type is not allowed and no explicit taxonomy override exists, output `docs blocked`.

Create a new `docs/**` file only when it would reduce future code/docs alignment mistakes. A new project doc is appropriate when at least one is true:

- The knowledge affects future code changes.
- The boundary or design intent is not obvious from code.
- The area is an established public surface, architecture boundary, or repeated execution context.
- The same confusion, drift, or planning discussion has appeared more than once.
- The source is an explicit `.session/threads/**` artifact or a clear code/diff/test fact.

If the content is temporary, exploratory, unconfirmed, already obvious from code, or unlikely to affect future execution, output `docs blocked` and recommend `persist` to `.session/**` or explicit `notes/**`.

## Instructions

Use `sync` only for code-aligned project docs and code-adjacent README alignment. Inputs may come from `.session/**`, source code, diffs, tests, or existing docs, but outputs are limited to allowed project docs targets and `src/**/README.md`.

Use session artifacts only when the user explicitly names the `.session/threads/**` source or otherwise confirms it as source of truth. If the only available source is `.session/inbox/**` or `notes/**`, block until the user confirms source of truth or routes the material through `review`, `shape`, or `persist` into a thread artifact.

`review` makes the judgment; `sync` performs the projection. If the request asks which source is correct, whether the design is good, whether code should change, or whether docs should override code, route to `review` or `shape` instead of deciding inside `sync`.

If `Target` exists, update only that target. If `Scope` exists without a target, inspect related docs/code and output an `Alignment Set` of at most 3 target candidates. If more than 3 targets are needed, split the work into batches and do not write them all at once.

## Output Rules

- Project docs go only to allowed project docs targets unless an explicit taxonomy override is provided.
- Code-adjacent README files go to `src/**/README.md`.
- Do not write `.session/**`.
- Do not write `notes/**`.
- Do not create workflow-internal docs under `docs/**` by default.
- For scope-based sync, output `Alignment Set` before writing. Each item must include docs type, target, source, source of truth, and alignment reason.
- Default alignment set size is 1-3 targets. More than 3 targets must be split into batches.
- Include `Blocked Items` for unsupported docs types, workflow-internal targets, unclear source of truth, or missing alignment criteria.
- Include `Follow-up Review Needed` when source-of-truth judgment is required.
- Project docs source from session must be explicit, usually `.session/threads/**`; inbox notes and exploration notes require explicit source-of-truth confirmation.
- With `consistency`, sync only confirmed drift. If code may be wrong, route to `plan -> build` or external-agent implementation; if intent is unclear, route to `shape`.
- With `architecture`, project constraints go to `docs/architecture/boundaries.md` or a user-specified docs target.
- With `language`, settled terminology goes to `docs/reference/glossary.md` or a user-specified allowed docs target.
- With `distill`, reference-derived structures go to project docs only after they are confirmed as project knowledge.

## User Input

{{project doc update, code-adjacent README request, diff, session decision, or docs/code drift concern}}
