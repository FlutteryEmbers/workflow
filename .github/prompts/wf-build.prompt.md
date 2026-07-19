---
description: Workflow Lite build command for explicit-plan execution.
argument-hint: "Plan=<plan path or pasted explicit plan>; Lens=<none|test|debug>; Request=<build constraints or target segment>"
---

# wf-build

Use Workflow Lite build semantics.

Mode: execute
Task: build
Lens: ${input:lens:none}
Plan: ${input:plan:required explicit executable plan}

Rules:
- Use one standard response grouped as `User Intent`, `Task State`, `Primary Result`, `Supporting Information`, `Next`, and optional `Persistence`; omit optional empty groups.
- This command is an explicit execution entrypoint. User invocation authorizes execution of the provided plan only.
- Require `Plan:` as a file path, pasted explicit plan, or complete plan in the current prompt.
- Do not use `notes/**` as an execution plan source.
- If the plan is missing target files, allowed changes, do-not-touch areas, step verification, or stop conditions, return `missing_prerequisite` and do not edit files.
- Before verification, establish `Execution Environment Contract`: CWD, repo root, OS/shell, package manager or runner, available scripts, verification command source, and retry budget.
- Verification commands must have `Command Provenance`: plan, repo script, Makefile, project docs, CI, or confirmed repo fact.
- Do not blindly retry path, cwd, shell, quoting, or command variants. Default retry budget is 2 for the same command/path/shell failure class.
- Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.
- Do not modify `.session/**`, `.workflow/**`, `docs/**`, `.github/**`, or unrelated files unless the plan explicitly lists them.
- Stop if implementation requires scope expansion, unplanned compatibility removal, unplanned alias/migration/fallback removal, or unplanned constraint override.
- Return a concise `Execution Trace` for ordinary successful execution.
- Automatically add expanded trace evidence when blocked, partial, verification failed, a pitfall was found, reusable execution discovery appeared, scope expansion risk appeared, or the user asks to persist the trace.
- Record pitfalls as execution facts with `Likely Source`, not review verdicts.
- Do not write `.session/**`; if useful, output `Suggested Persist Candidate` for current-work-item audit as `Artifact=note; Intent=audit; Topic=<topic>_execution_trace`, or reusable discovery as `Artifact=note; Artifact State=inbox; Intent=capture; Topic=<topic>_execution_discovery`.
- Use `.workflow/tasks/build.md` as the task contract.

Request:
${input:request:optional build constraints, target segment, or verification notes}

Return:
- `User Intent`: explicit plan and requested execution outcome.
- `Task State`: Result, Review Status, Risk Notice, and Boundary Advice or stop reason when relevant.
- `Primary Result`: concise Execution Trace.
- `Supporting Information`: environment and expanded changed files, completed/skipped steps, verification, deviations, pitfalls, and reusable discoveries only when triggered.
- `Next`: docs follow-up and execution follow-up when relevant.
- `Persistence`: Suggested Persist Candidate only when worth saving; build does not write it.
