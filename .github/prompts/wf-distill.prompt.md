---
description: Workflow Lite distill command for user-directed summaries.
argument-hint: "Source=<file, folder, thread, docs, discussion, or reference>; Summary Focus=<what to summarize>; Summary Type=<structure-summary|folder-summary|content-summary|decision-summary|interface-summary|risk-summary|archive-summary-draft>; Lens=<none|language>; Request=<distill request>"
---

# wf-distill

Use Workflow Lite distill semantics.

Mode: discuss
Task: distill
Lens: ${input:lens:none}
Source: ${input:source:source material to summarize}
Summary Focus: ${input:summary_focus:what dimension to summarize}
Summary Type: ${input:summary_type:structure-summary|folder-summary|content-summary|decision-summary|interface-summary|risk-summary|archive-summary-draft}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
- Do not write files.
- Do not load templates.
- Load `.workflow/lenses/language.md` only when the user explicitly selects `language`.
- Use `.workflow/tasks/distill.md` as the task contract.
- Summarize only the requested source and focus.
- Separate `Observed`, `Inferred`, and `Unknown`.
- Do not judge accuracy, source of truth, or readiness; route those to `review`.
- You may include next use, persist candidate, review suggestion, or sync/archive handoff hint, but do not write or judge source-of-truth.
- For direct thread archive writes, route to `sync session-archive`; `archive-summary-draft` is only a draft.

Request:
${input:request:describe the summary or distillation needed}

Return:
- `User Intent`: summary request.
- `Task State`: Source Boundaries, Summary Focus, and Summary Type.
- `Primary Result`: Summary, Observed, Inferred, and Unknown.
- `Supporting Information`: Omitted / Out Of Scope when relevant.
- `Next`: Next Use and Recommended Next Task.
- `Persistence`: Persist Candidate only when worth saving; candidate only and do not write.
