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
Lens: <none|domain|test|architecture|change|knowledge|debug>
Context:
- #workflow_core/tasks/<task>.md
- #workflow_core/templates/<template>.md
- #target/file/or/doc
Request:
<what you want>
```

## Common Context Sets

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
- #workflow_core/lenses/knowledge.md
- #workflow_core/lenses/debug.md

### Shape A Solution

Add:

- #workflow_core/tasks/shape.md
- #workflow_core/templates/shape.md
- relevant brief, note, code, or decision context

Optional user-selected lenses:

- #workflow_core/lenses/domain.md
- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/test.md
- #workflow_core/lenses/change.md

### Make A Plan

Add:

- #workflow_core/tasks/plan.md
- #workflow_core/templates/plan.md
- relevant brief, shape, review, or requested work

Optional user-selected lenses:

- #workflow_core/lenses/test.md
- #workflow_core/lenses/architecture.md
- #workflow_core/lenses/change.md

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
- #workflow_core/lenses/domain.md
- #workflow_core/lenses/architecture.md
