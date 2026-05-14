# Copilot Context Guide

Use this file as a menu for manual Add Context. Prefer one task plus only the files needed for the request.

## Principle

- Use exactly one task as the main workflow context.
- Lenses are user-selected. Do not apply a lens unless the user names it or adds its file.
- If no lens is named, use `Lens: none`.
- Do not add all task, template, role, or lens files.

## Prompt Pattern

```text
Task: <route|clarify|explore|shape|plan|build|review|sync>
Lens: <none|iteration|expand|language|domain|strategy|redteam|test|architecture|change|knowledge|debug>
Context:
- #workflow_core/tasks/<task>.md
- #workflow_core/templates/<template>.md
- #target/file/or/doc
Request:
<what you want>
```

## Common Context Sets

### Ask For Usage Guidance

Use this when the user wants a chat-only recommendation for task order, lenses, Add Context, and the next prompt.

Add:

- #workflow_core/tasks/route.md
- #workflow_core/copilot.md only when the user wants Copilot-specific Add Context guidance

Do not add all task or lens files. `route` may recommend lenses, but must not apply them automatically.

### Clarify A Request

Add:

- #workflow_core/tasks/clarify.md
- #workflow_core/templates/brief.md

Optional user-selected lenses:

- #workflow_core/lenses/domain.md
- #workflow_core/lenses/change.md
- #workflow_core/lenses/knowledge.md

### Explore Code Or Material

Add:

- #workflow_core/tasks/explore.md
- #workflow_core/templates/note.md
- relevant source files, docs, or notes

Optional user-selected lenses:

- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/strategy.md
- #workflow_core/lenses/knowledge.md
- #workflow_core/lenses/debug.md

### Shape A Solution

Add:

- #workflow_core/tasks/shape.md
- #workflow_core/templates/shape.md
- relevant brief, note, code, or decision context

Optional user-selected lenses:

- #workflow_core/lenses/iteration.md
- #workflow_core/lenses/expand.md
- #workflow_core/lenses/language.md
- #workflow_core/lenses/domain.md
- #workflow_core/lenses/strategy.md
- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/test.md
- #workflow_core/lenses/change.md

### Make A Plan

Add:

- #workflow_core/tasks/plan.md
- #workflow_core/templates/plan.md
- relevant brief, shape, review, or requested work

Optional user-selected lenses:

- #workflow_core/lenses/iteration.md
- #workflow_core/lenses/expand.md
- #workflow_core/lenses/language.md
- #workflow_core/lenses/strategy.md
- #workflow_core/lenses/test.md
- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/change.md

### Expand A Shape Or Plan

Use this when a short shape, conceptual design, architecture shape, or plan needs more detail, examples, pseudocode, smaller diagrams, or split part files.

Add:

- #workflow_core/tasks/shape.md or #workflow_core/tasks/plan.md as the main task for this turn
- #workflow_core/templates/expanded.md
- the source shape, decision, concept, or plan to expand
- any relevant notes, code, or docs needed to avoid inventing facts

Optional user-selected lenses:

- #workflow_core/lenses/expand.md
- #workflow_core/lenses/language.md only if full English, translation, or terminology consistency is requested
- #workflow_core/lenses/architecture.md only if architecture detail is requested
- #workflow_core/lenses/domain.md only if language, rules, events, or ownership detail is requested
- #workflow_core/lenses/test.md only if verification detail is requested

Output follows the source artifact directory, using `{base}.expanded.md` and optional `{base}.part_{topic}.md`. Do not create expansion-specific directories.

### Target Design To Repo Plan

Use this when the user has a target architecture or conceptual design and wants a repo-aware implementation plan.

For the planning turn, make #workflow_core/tasks/plan.md the main task. Add only the files needed for the current turn.

Add:

- #workflow_core/tasks/plan.md
- #workflow_core/templates/plan.md
- target design artifacts such as #workflow_core/templates/shape.md or #workflow_core/templates/concept.md
- repo-mapping notes such as #workflow_core/templates/note.md or existing `.docs/work/notes/**`
- relevant source files, module docs, `.docs/work/**`, or `.docs/current/**`

Optional user-selected lenses:

- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/domain.md
- #workflow_core/lenses/iteration.md
- #workflow_core/lenses/strategy.md

If the target design still needs shaping, use #workflow_core/tasks/shape.md for that turn first. If repo reality is still unclear, use #workflow_core/tasks/explore.md for that turn first.

### Build From A Plan

Add:

- #workflow_core/tasks/build.md
- approved plan file
- target source files

Optional user-selected lenses:

- #workflow_core/lenses/test.md
- #workflow_core/lenses/debug.md
- #workflow_core/lenses/change.md

### Review Or Refactor

Add:

- #workflow_core/tasks/review.md
- #workflow_core/templates/review.md
- target source files, docs, behavior claim, or evidence

Optional user-selected lenses:

- #workflow_core/lenses/redteam.md
- #workflow_core/lenses/language.md
- #workflow_core/lenses/domain.md
- #workflow_core/lenses/debug.md
- #workflow_core/lenses/test.md
- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/knowledge.md
- #workflow_core/lenses/change.md

### Sync Docs Or Knowledge

Add:

- #workflow_core/tasks/sync.md
- #workflow_core/templates/sync.md
- source doc, change record, code-adjacent doc, or knowledge material

Optional user-selected lenses:

- #workflow_core/lenses/change.md
- #workflow_core/lenses/knowledge.md
- #workflow_core/lenses/language.md
- #workflow_core/lenses/domain.md
- #workflow_core/lenses/architecture.md

### Language Style

Use this when the user asks for full English, translation, terminology normalization, readability review, or glossary cleanup.

Add:

- one main task for the current turn, usually #workflow_core/tasks/shape.md, #workflow_core/tasks/plan.md, #workflow_core/tasks/review.md, or #workflow_core/tasks/sync.md
- #workflow_core/lenses/language.md
- target docs or artifacts whose language style should be controlled

Default artifacts use Chinese explanations with English technical terms preserved. Full English is used only when explicitly requested.

## Conversation-Driven Design

Use these sets when the user is iterating with AI across multiple turns, adding background, comparing options, challenging gaps, and then syncing only stable conclusions.

### Start Or Maintain A Design Session

Add:

- #workflow_core/tasks/shape.md
- #workflow_core/templates/session.md
- #workflow_core/templates/options.md
- #workflow_core/templates/concept.md only when a conceptual model is needed
- #workflow_core/lenses/iteration.md
- #workflow_core/lenses/strategy.md
- #workflow_core/lenses/domain.md only when the user wants DDD-lite language, story, events, boundaries, and rule ownership
- current brief, notes, code, or discussion summary

### Challenge The Current Direction

Add:

- #workflow_core/tasks/review.md
- #workflow_core/templates/critique.md
- #workflow_core/lenses/redteam.md
- #workflow_core/lenses/domain.md only when critique should inspect language, story flow, events, boundaries, or rule ownership
- current session, options, concept, shape, or plan artifact

### Sync Stable Conclusions

Add:

- #workflow_core/tasks/sync.md
- #workflow_core/templates/sync.md
- #workflow_core/lenses/knowledge.md when the result should become reusable knowledge
- session, options, critique, concept, shape, or decision artifacts

Only sync stable conclusions. Do not archive full chat transcripts as durable facts.
