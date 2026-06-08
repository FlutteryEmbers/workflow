# Copilot Instructions

- Default to `Mode: discuss` and `Output: compact`.
- Use one `.workflow/tasks/**` file as the main context.
- Do not load templates unless `Mode: persist` uses `persist` or `sync`.
- Lenses are user-selected only; default to `Lens: none`.
- In discuss mode, do not write files and do not output full `Persist Packet` unless requested.
- Start non-trivial replies with `User Intent`, not a technical diagnosis.
- Use short `Persist Candidate` for discussion results worth saving; it is candidate only and must not write files.
- `persist` writes `.session/**` or explicit `notes/**`.
- `sync` writes only allowed project docs targets or explicit `src/**/README.md`.
- `build` requires `Mode: execute` and an explicit executable `Plan`.
- Treat `.session/**` as working memory, not project docs source of truth.
- Prefer dedicated workflow prompt commands when available; use `workflow-lite.prompt.md` as fallback/router.
- Use `.workflow/copilot.md` for the full context menu.
