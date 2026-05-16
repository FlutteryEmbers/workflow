---
description: Use Workflow Lite with explicit user-selected lenses.
argument-hint: "Mode=<discuss|persist|execute>; Task=<route|clarify|explore|shape|plan|build|review|sync>; Lens=<none|iteration|expand|consistency|distill|language|domain|strategy|redteam|test|architecture|change|knowledge|debug>; Target=<required for persist>; Plan=<required for execute>; Request=<what you want>"
---

# Workflow Lite Prompt

Use Workflow Lite with progressive context.

## Required Input

```text
Mode: ${input:mode:discuss}
Task: ${input:task:route|clarify|explore|shape|plan|build|review|sync}
Lens: ${input:lens:none; comma-separated lenses allowed only when explicitly selected}
Target: ${input:target:required for persist; otherwise none}
Plan: ${input:plan:required for execute; otherwise none}
Request: ${input:request:describe the work}
```

## Rules

- Use exactly one task as the main workflow context.
- Default to `Mode: discuss`.
- Start with an inline `Understanding Check` unless the request is trivial.
- Ask for confirmation only when ambiguity would affect file writes, execution, or material scope.
- Use `Lens: none` unless the user explicitly names or adds lens files.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed; follow the user's lens order.
- Do not infer, auto-apply, or load all lenses.
- In `Mode: discuss`, do not load templates and do not create or update files.
- In `Mode: persist`, load the matching template and write only the requested target.
- In `Mode: execute`, require `Task: build` and an approved plan.
- Block instead of writing when `Mode: persist` lacks `Target`, `Mode: execute` lacks `Plan`, the target is outside the mode boundary, or instructions conflict.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Copilot may recommend additional lenses for a follow-up turn.
- Keep ordinary outputs in `.docs/work/**`.

## Context Format

Add the selected task file from `.workflow/tasks/`.
Add the matching template from `.workflow/templates/` only in `Mode: persist`.
Add selected lens files from `.workflow/lenses/` only when `Lens` is not `none`.
Add target source files or existing `.docs/**` files relevant to the request.
