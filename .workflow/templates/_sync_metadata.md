## Sync Metadata

- Sync Domain: {{project-docs | session-archive}}
- Sync Object: {{architecture | feature | reference | code-readme | archive-summary}}
- Source Of Truth: {{code | diff | session thread artifact | explicit user decision | existing docs}}
- Source: {{source path, artifact, diff, code path, thread, or docs path}}
- Target: {{docs/** | src/**/README.md | .session/archive/<thread>/summary.md}}
- Updated At: {{YYYY-MM-DD HH:mm}}
- Timezone: {{IANA timezone or UTC offset}}

Archive-only fields:

- Source Thread: {{.session/threads/<thread>/** or none}}
- Thread Status: {{settled | superseded | abandoned | implemented | blocked | none}}
- Archive Purpose: {{why this thread is being archived, or none}}
- Summary Scope: {{source artifacts and time span, or none}}
- Next Retrieval Use: {{how a future reader should use this archive, or none}}
