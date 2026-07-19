# Copilot Instructions

- Default to `Mode: discuss` and the task's single standard Response Contract.
- Use one `.workflow/tasks/**` file as the main context.
- Do not load templates unless `Mode: persist` uses `persist` or `sync`.
- Lenses are user-selected only; default to `Lens: none`.
- In discuss mode, do not write files or construct artifact handoff packets; output `Persist Candidate` only when worth saving.
- Start non-trivial replies with `User Intent`, not a technical diagnosis.
- Group responses as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`.
- Use short `Persist Candidate` for discussion results worth saving; it is candidate only and must not write files.
- Use `vscode/askQuestions` only for either a `shape` `Human Decision State: checkpoint` or a triggered `plan` `Compatibility Intake`.
- For `Compatibility Intake`, complete repo preflight first, ask the compatibility-only questions defined by `.workflow/tasks/plan.md`, then continue planning after the answers. Do not use the native question UI instead of repo preflight, `explore`, `review`, ordinary planning, or any write/execute authorization.
- `persist` writes active `.session/inbox/**` and `.session/threads/**` artifacts or explicit `notes/**`; `.session/archive/**` is a sync target.
- `persist` must load the matching artifact template and metadata partial and write every required template section; `Depth` changes content density only.
- `sync` writes only stable-document targets: allowed project docs, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.
- `build` requires `Mode: execute` and an explicit executable `Plan`.
- Treat `.session/**` as working memory, not project docs source of truth.
- Prefer dedicated workflow prompt commands when available; use `workflow-lite.prompt.md` as fallback/router.
- Use `.workflow/copilot.md` for the full context menu.
