# Copilot Instructions

- Default to `Mode: discuss` and `Output: compact`.
- Use one `.workflow/tasks/**` file as the main context.
- Do not load templates unless `Mode: persist` uses `persist` or `sync`.
- Lenses are user-selected only; default to `Lens: none`.
- In discuss mode, do not write files and do not output full `Persist Packet` unless requested.
- Use short `Persist Hint` for discussion results worth saving.
- `persist` writes `.session/**` or explicit `notes/**`.
- `sync` writes only `docs/**` or explicit `src/**/README.md`.
- `build` requires `Mode: execute` and an explicit executable `Plan`.
- Treat `.session/**` as working memory, not project docs source of truth.
- Use `.workflow/copilot.md` for the full context menu.
