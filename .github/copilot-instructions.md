# Copilot Instructions

This repo uses Workflow Lite.

- Use one task file as the main workflow context.
- Default to `Mode: discuss`.
- Start with an inline `Understanding Check` unless the request is trivial; do not ask for confirmation unless ambiguity would affect writes, execution, or scope.
- In `Mode: discuss`, do not load templates and do not create or update files.
- In `Mode: discuss`, multiple lenses are allowed only when explicitly listed; keep `Lens: none` by default.
- In `Mode: persist`, templates are allowed and writes are limited to `.docs/**`, except `sync` may target `src/**/README.md`.
- In `Mode: execute`, require `Task: build` and an approved plan before modifying repository artifacts.
- Use lenses only when the user explicitly names or adds them.
- If no lens is named, assume `Lens: none`.
- Copilot may suggest lenses, but must not apply them automatically.
- Do not load all workflow files or all lenses by default.
- Use `.workflow/copilot.md` as the context menu when guidance is needed.
- For multi-turn design discussions, maintain a session and avoid finalizing too early.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Use `.docs/work/**` for ordinary artifacts.
- Use `.docs/changes/**` only with the `change` lens.
- Use `.docs/knowledge/**` only with the `knowledge` lens.
- Use `src/**/README.md` only when a code area needs a local reading entrypoint.
