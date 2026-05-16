# Copilot Context Guide

Use this file as a menu for manual Add Context. Prefer one task plus only the files needed for the request.

## Principle

- Use exactly one task as the main workflow context.
- Lenses are user-selected. Do not apply a lens unless the user names it or adds its file.
- If no lens is named, use `Lens: none`.
- Do not add all task, template, role, or lens files.

## Prompt Format

```text
Task: <route|clarify|explore|shape|plan|build|review|sync>
Lens: <none|iteration|expand|distill|language|domain|strategy|redteam|test|architecture|change|knowledge|debug>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/templates/<template>.md
- #target/file/or/doc
Request:
<what you want>
```

## Common Context Sets

### Ask For Usage Guidance

Use this when the user wants a chat-only recommendation for task order, lenses, Add Context, and the next prompt.

Add:

- #.workflow/tasks/route.md
- #.workflow/copilot.md only when the user wants Copilot-specific Add Context guidance

Do not add all task or lens files. `route` may recommend lenses, but must not apply them automatically.

### Clarify A Request

Add:

- #.workflow/tasks/clarify.md
- #.workflow/templates/brief.md

Optional user-selected lenses:

- #.workflow/lenses/domain.md
- #.workflow/lenses/change.md
- #.workflow/lenses/knowledge.md

### Explore Code Or Material

Add:

- #.workflow/tasks/explore.md
- #.workflow/templates/note.md
- relevant source files, docs, or notes

Optional user-selected lenses:

- #.workflow/lenses/distill.md
- #.workflow/lenses/architecture.md
- #.workflow/lenses/strategy.md
- #.workflow/lenses/knowledge.md
- #.workflow/lenses/debug.md

### Shape A Solution

Add:

- #.workflow/tasks/shape.md
- #.workflow/templates/shape.md
- relevant brief, note, code, or decision context

Optional user-selected lenses:

- #.workflow/lenses/iteration.md
- #.workflow/lenses/expand.md
- #.workflow/lenses/distill.md
- #.workflow/lenses/language.md
- #.workflow/lenses/domain.md
- #.workflow/lenses/strategy.md
- #.workflow/lenses/architecture.md
- #.workflow/lenses/test.md
- #.workflow/lenses/change.md

### Make A Plan

Add:

- #.workflow/tasks/plan.md
- #.workflow/templates/plan.md
- relevant brief, shape, review, or requested work

Optional user-selected lenses:

- #.workflow/lenses/iteration.md
- #.workflow/lenses/expand.md
- #.workflow/lenses/language.md
- #.workflow/lenses/strategy.md
- #.workflow/lenses/test.md
- #.workflow/lenses/architecture.md
- #.workflow/lenses/change.md

### Expand A Shape Or Plan

Use this when a short shape, conceptual design, architecture shape, or plan needs more detail, examples, pseudocode, smaller diagrams, or split part files.

Add:

- #.workflow/tasks/shape.md or #.workflow/tasks/plan.md as the main task for this turn
- #.workflow/templates/expanded.md
- the source shape, decision, concept, or plan to expand
- any relevant notes, code, or docs needed to avoid inventing facts

Optional user-selected lenses:

- #.workflow/lenses/expand.md
- #.workflow/lenses/language.md only if full English, translation, or terminology consistency is requested
- #.workflow/lenses/architecture.md only if architecture detail is requested
- #.workflow/lenses/domain.md only if language, rules, events, or ownership detail is requested
- #.workflow/lenses/test.md only if verification detail is requested

Output follows the source artifact directory, using `{base}.expanded.md` and optional `{base}.part_{topic}.md`. Do not create expansion-specific directories.

### Target Design To Repo Plan

Use this when the user has a target architecture or conceptual design and wants a repo-aware implementation plan.

For the planning turn, make #.workflow/tasks/plan.md the main task. Add only the files needed for the current turn.

Add:

- #.workflow/tasks/plan.md
- #.workflow/templates/plan.md
- target design artifacts such as #.workflow/templates/shape.md or #.workflow/templates/concept.md
- repo-mapping notes such as #.workflow/templates/note.md or existing `.docs/work/notes/**`
- relevant source files, module docs, `.docs/work/**`, or `.docs/current/**`

Optional user-selected lenses:

- #.workflow/lenses/architecture.md
- #.workflow/lenses/domain.md
- #.workflow/lenses/iteration.md
- #.workflow/lenses/strategy.md

If the target design still needs shaping, use #.workflow/tasks/shape.md for that turn first. If repo reality is still unclear, use #.workflow/tasks/explore.md for that turn first.

### Build From A Plan

Add:

- #.workflow/tasks/build.md
- approved plan file
- target source files, docs, prompts, templates, or workflow artifacts

Optional user-selected lenses:

- #.workflow/lenses/test.md
- #.workflow/lenses/debug.md
- #.workflow/lenses/change.md

### Review Or Refactor

Add:

- #.workflow/tasks/review.md
- #.workflow/templates/review.md
- target source files, docs, behavior claim, or evidence

Optional user-selected lenses:

- #.workflow/lenses/redteam.md
- #.workflow/lenses/distill.md
- #.workflow/lenses/language.md
- #.workflow/lenses/domain.md
- #.workflow/lenses/debug.md
- #.workflow/lenses/test.md
- #.workflow/lenses/architecture.md
- #.workflow/lenses/knowledge.md
- #.workflow/lenses/change.md

### Sync Docs Or Knowledge

Add:

- #.workflow/tasks/sync.md
- #.workflow/templates/sync.md
- source doc, change record, code-adjacent doc, or knowledge material

Optional user-selected lenses:

- #.workflow/lenses/change.md
- #.workflow/lenses/knowledge.md
- #.workflow/lenses/distill.md
- #.workflow/lenses/language.md
- #.workflow/lenses/domain.md
- #.workflow/lenses/architecture.md

### Distill A Reference

Use this when a strong business document, architecture directory, RFC, ADR, handbook, or knowledge base feels useful, but the reusable structure is not obvious yet.

For pure knowledge capture, use `explore --lens distill --lens knowledge -> sync --lens knowledge`.

For improving Workflow Lite itself, use `explore --lens distill -> shape --lens distill --lens strategy -> plan -> build -> review`.

Add:

- #.workflow/tasks/explore.md as the main task for first-pass extraction
- #.workflow/templates/distillation.md
- #.workflow/lenses/distill.md
- the reference document, directory, pasted material, or existing `.docs/**` artifact
- #.workflow/lenses/knowledge.md only when the result should become durable knowledge

Optional follow-up context:

- #.workflow/tasks/shape.md and #.workflow/lenses/strategy.md when deciding what to adopt, adapt, reject, or revisit
- #.workflow/tasks/plan.md when the chosen improvement should become an approved plan
- #.workflow/tasks/build.md only after the approved plan explicitly names the repository artifacts to modify

Do not use `sync` to directly edit `.workflow/**`. Template, lens, prompt, or workflow behavior changes must go through `plan -> build`.

### Language Style

Use this when the user asks for full English, translation, terminology normalization, readability review, or glossary cleanup.

Add:

- one main task for the current turn, usually #.workflow/tasks/shape.md, #.workflow/tasks/plan.md, #.workflow/tasks/review.md, or #.workflow/tasks/sync.md
- #.workflow/lenses/language.md
- target docs or artifacts whose language style should be controlled

Default artifacts use Chinese explanations with English technical terms preserved. Full English is used only when explicitly requested.

## Conversation-Driven Design

Use these sets when the user is iterating with AI across multiple turns, adding background, comparing options, challenging gaps, and then syncing only stable conclusions.

### Start Or Maintain A Design Session

Add:

- #.workflow/tasks/shape.md
- #.workflow/templates/session.md
- #.workflow/templates/options.md
- #.workflow/templates/concept.md only when a conceptual model is needed
- #.workflow/lenses/iteration.md
- #.workflow/lenses/strategy.md
- #.workflow/lenses/domain.md only when the user wants DDD-lite language, story, events, boundaries, and rule ownership
- current brief, notes, code, or discussion summary

### Challenge The Current Direction

Add:

- #.workflow/tasks/review.md
- #.workflow/templates/critique.md
- #.workflow/lenses/redteam.md
- #.workflow/lenses/domain.md only when critique should inspect language, story flow, events, boundaries, or rule ownership
- current session, options, concept, shape, or plan artifact

### Sync Stable Conclusions

Add:

- #.workflow/tasks/sync.md
- #.workflow/templates/sync.md
- #.workflow/lenses/knowledge.md when the result should become reusable knowledge
- session, options, critique, concept, shape, or decision artifacts

Only sync stable conclusions. Do not archive full chat transcripts as durable facts.
