---
description: Use Workflow Lite with explicit user-selected lenses.
argument-hint: "Task=<route|clarify|explore|shape|plan|build|review|sync>; Lens=<none|iteration|expand|language|domain|strategy|redteam|test|architecture|change|knowledge|debug>; Request=<what you want>"
---

# Workflow Lite Prompt

Use Workflow Lite with progressive context.

## Required Input

```text
Task: ${input:task:route|clarify|explore|shape|plan|build|review|sync}
Lens: ${input:lens:none}
Request: ${input:request:describe the work}
```

## Rules

- Use exactly one task as the main workflow context.
- Use `Lens: none` unless the user explicitly names or adds lens files.
- Do not infer, auto-apply, or load all lenses.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Copilot may recommend additional lenses for a follow-up turn.
- Keep ordinary outputs in `.docs/work/**`.

## Context Pattern

Add the selected task file from `.workflow/tasks/`.
Add the matching template from `.workflow/templates/` only when producing a file artifact.
Add selected lens files from `.workflow/lenses/` only when `Lens` is not `none`.
Add target source files or existing `.docs/**` files relevant to the request.
