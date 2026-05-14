# Copilot Instructions

This repo uses Workflow Lite.

- Use one task file as the main workflow context.
- Use lenses only when the user explicitly names or adds them.
- If no lens is named, assume `Lens: none`.
- Copilot may suggest lenses, but must not apply them automatically.
- Do not load all workflow files or all lenses by default.
- Use `.docs/work/**` for ordinary artifacts.
- Use `.docs/changes/**` only with the `change` lens.
- Use `.docs/knowledge/**` only with the `knowledge` lens.
- Keep `src/**/MODULE.md` next to code.
