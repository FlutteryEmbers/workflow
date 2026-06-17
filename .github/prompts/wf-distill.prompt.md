---
description: Workflow Lite distill command for user-directed summaries.
argument-hint: "Source=<file, folder, thread, docs, discussion, or reference>; Summary Focus=<what to summarize>; Summary Type=<structure-summary|folder-summary|content-summary|decision-summary|interface-summary|risk-summary|archive-summary-draft>; Output=<compact|normal|full>; Request=<distill request>"
---

# wf-distill

Use Workflow Lite distill semantics.

Mode: discuss
Output: ${input:output:compact|normal|full}
Task: distill
Lens: none
Source: ${input:source:source material to summarize}
Summary Focus: ${input:summary_focus:what dimension to summarize}
Summary Type: ${input:summary_type:structure-summary|folder-summary|content-summary|decision-summary|interface-summary|risk-summary|archive-summary-draft}

Rules:
- Do not write files.
- Do not load templates.
- Use `.workflow/tasks/distill.md` as the task contract.
- Summarize only the requested source and focus.
- Separate `Observed`, `Inferred`, and `Unknown`.
- Do not judge accuracy, source of truth, or readiness; route those to `review`.
- You may include next use, persist candidate, review suggestion, or sync/archive handoff hint, but do not write or judge source-of-truth.
- For direct thread archive writes, route to `sync session-archive`; `archive-summary-draft` is only a draft.

Request:
${input:request:describe the summary or distillation needed}

Return:
- User Intent
- Source Scope
- Summary Focus
- Summary Type
- Summary
- Observed
- Inferred
- Unknown
- Next Use
- Recommended Next Task
- Persist Candidate, candidate only and do not write
