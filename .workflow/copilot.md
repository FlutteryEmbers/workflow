# Copilot Context Guide

Use this file as a menu for manual Add Context. Prefer one mode, one task, selected lenses, and only the files needed for the current request.

## Mode Format

```text
Mode: <discuss|persist|execute>
Task: <route|clarify|explore|shape|plan|build|review|sync>
Lens: <none|iteration|expand|consistency|distill|language|domain|strategy|redteam|test|architecture|change|knowledge|debug>
Target: <required for persist; otherwise none>
Plan: <required for execute; otherwise none>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/lenses/<lens>.md only when selected
- #.workflow/templates/<template>.md only in Mode: persist
- #target/file/or/doc
Request:
<what you want>
```

## Mode Rules

- `Mode: discuss` is the default. Add the task, selected lenses, and relevant context. Do not add templates. Do not create or update files.
- `Mode: persist` writes a documentation artifact. Add the task, matching template, selected lenses, source context, and `Target`.
- `Mode: execute` applies an approved plan. Use `Task: build`, add the approved plan, selected lenses, and target files or artifacts.
- `Lens: change` still means tracked work under `.docs/changes/{change_id}`. It is not an execution mode.

## Lens Selection Rules

- Default to `Lens: none`.
- Multiple lenses are allowed in `Mode: discuss` only when the user explicitly lists them.
- Copilot may recommend lenses, but must not load or apply them automatically.
- In multi-lens discuss, organize output in the user's lens order, then provide a converged recommendation and suggested persist step.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens. If more lenses are needed, split into multiple persist steps.
- In `Mode: execute`, prefer execution guardrail lenses such as `test`, `debug`, or `change`; do not reopen strategy, domain, or architecture decisions during execution.

## Understanding Check

- Start with an inline `## Understanding` unless the request is trivial.
- Keep it short: one sentence for simple work, up to three bullets for complex work.
- Include mode, task, selected lenses, target or plan, and key constraints when relevant.
- Do not ask for confirmation by default; continue in the same response.
- Ask before proceeding only when ambiguity would affect file writes, execution, scope, target, plan, or source of truth.

For `Mode: execute`, use `## Execution Understanding` and name the approved plan before changing files.

## Write Boundaries

- `discuss`: no writes.
- `persist`: write `.docs/**`; only `Task: sync` may write `src/**/README.md`.
- `execute`: may modify broader repository artifacts only when the approved plan says so.

## Discuss Context

Use this for brainstorming, comparison, critique, route guidance, code explanation, or early planning.

Add:

- #.workflow/tasks/<task>.md
- selected #.workflow/lenses/<lens>.md only when the user names it; multiple explicit lenses are allowed
- relevant source files, docs, or existing `.docs/**`

Do not add:

- #.workflow/templates/**

## Persist Context

Use this when the user says save, write, generate, update, land, or persist an artifact.

Add:

- #.workflow/tasks/<task>.md
- the matching #.workflow/templates/<template>.md
- selected #.workflow/lenses/<lens>.md only when the user names it
- `Target: <path>`
- source context needed to avoid inventing facts

Allowed targets:

- `.docs/**` for `clarify`, `explore`, `shape`, `plan`, `review`, and `sync`
- `src/**/README.md` only for `Task: sync`

If `Target` is missing, propose a target path in chat instead of writing.

## Execute Context

Use this when the user asks to apply an approved plan.

Add:

- #.workflow/tasks/build.md
- approved plan file
- target source files, docs, prompts, templates, or workflow artifacts named by the plan
- selected #.workflow/lenses/test.md, #.workflow/lenses/debug.md, or #.workflow/lenses/change.md only when selected

If an approved plan is missing, do not modify files. Explain what plan is needed.

## Task To Template Map

- `clarify`: #.workflow/templates/brief.md
- `explore`: #.workflow/templates/note.md; with `distill` use #.workflow/templates/distillation.md; with `strategy` use #.workflow/templates/options.md
- `shape`: #.workflow/templates/shape.md; optional #.workflow/templates/session.md, #.workflow/templates/options.md, #.workflow/templates/concept.md, #.workflow/templates/expanded.md, or #.workflow/templates/distillation.md when the selected lens needs it
- `plan`: #.workflow/templates/plan.md; with `expand` use #.workflow/templates/expanded.md
- `review`: #.workflow/templates/review.md; with `redteam` use #.workflow/templates/critique.md; with `consistency` use #.workflow/templates/consistency_review.md
- `sync`: #.workflow/templates/sync.md; for `src/**/README.md` use #.workflow/templates/code_readme.md

## Common Scenarios

### Ask For Usage Guidance

```text
Mode: discuss
Task: route
Lens: none
```

Add #.workflow/tasks/route.md. Add #.workflow/copilot.md only when the user wants Copilot-specific Add Context guidance.

### Discuss A Design

```text
Mode: discuss
Task: shape
Lens: strategy
```

Add #.workflow/tasks/shape.md, selected lens files, and relevant code or docs. Do not add templates.

### Multi-Lens Discuss

```text
Mode: discuss
Task: shape
Lens: strategy, domain, architecture
```

Add #.workflow/tasks/shape.md plus the explicitly selected lens files. Do not add templates.

Recommended chat structure:

- `## Understanding`
- `## Strategy View`
- `## Domain View`
- `## Architecture View`
- `## Converged Recommendation`
- `## Suggested Persist Step`

### Persist A Shape Or Decision

```text
Mode: persist
Task: shape
Target: .docs/work/shapes/shape_{topic}.md
```

Add #.workflow/tasks/shape.md, #.workflow/templates/shape.md, selected lens files, and source context.

### Persist A Plan

```text
Mode: persist
Task: plan
Target: .docs/work/plans/plan_{topic}.md
```

Add #.workflow/tasks/plan.md, #.workflow/templates/plan.md, source shape/review/context, and selected lens files.

### Consistency Review

```text
Mode: persist
Task: review
Lens: consistency
Target: .docs/work/reviews/review_{topic}.md
```

Add #.workflow/tasks/review.md, #.workflow/lenses/consistency.md, #.workflow/templates/consistency_review.md, relevant `.docs/current/**`, `.docs/shared/**`, `src/**/README.md`, source files, and tests.

Follow-up paths:

- Document drift: `Mode: persist`, `Task: sync`
- Code drift: `Mode: persist`, `Task: plan`, then `Mode: execute`, `Task: build`
- Unclear intent: `Mode: discuss`, `Task: shape`

### Sync Durable Facts

```text
Mode: persist
Task: sync
Target: .docs/current/{topic}.md
```

Add #.workflow/tasks/sync.md, #.workflow/templates/sync.md, source artifacts, and selected lenses.

For code-adjacent README maintenance:

```text
Mode: persist
Task: sync
Target: src/{area}/README.md
```

Add #.workflow/templates/code_readme.md instead of the general sync template.

### Execute A Plan

```text
Mode: execute
Task: build
Plan: .docs/work/plans/plan_{topic}.md
```

Add #.workflow/tasks/build.md, the approved plan, and target artifacts named by the plan.

## Conversation-To-Artifact Example

1. Discuss:
   `Mode: discuss`, `Task: shape`, `Lens: strategy`, request a route comparison.
2. Persist shape:
   `Mode: persist`, `Task: shape`, `Target: .docs/work/shapes/shape_auth.md`.
3. Persist plan:
   `Mode: persist`, `Task: plan`, `Target: .docs/work/plans/plan_auth.md`.
4. Execute:
   `Mode: execute`, `Task: build`, `Plan: .docs/work/plans/plan_auth.md`.

In chat, summarize decisions and next steps. Do not paste full artifact contents unless the user asks for a preview.
