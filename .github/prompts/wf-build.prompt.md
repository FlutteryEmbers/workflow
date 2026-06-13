---
description: Workflow Lite build command for explicit-plan execution.
argument-hint: "Plan=<plan path or pasted explicit plan>; Lens=<none|test|debug>; Request=<build constraints or target segment>"
---

# wf-build

Use Workflow Lite build semantics.

Mode: execute
Output: compact unless blocked, partial, failed verification, pitfall found, reusable execution discovery appears, scope expansion risk appears, or full output is requested
Task: build
Lens: ${input:lens:none}
Plan: ${input:plan:required explicit executable plan}

Rules:
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
- Return compact `Execution Trace` by default.
- Use full `Execution Trace` only when blocked, partial, verification failed, a pitfall was found, reusable execution discovery appeared, scope expansion risk appeared, or the user asks to persist the trace.
- Record pitfalls as execution facts with `Likely Source`, not review verdicts.
- Do not write `.session/**`; if useful, output `Suggested Persist Candidate` for current-work-item audit as `Artifact=note; Intent=audit; Topic=<topic>_execution_trace`, or reusable discovery as `Artifact=note; Artifact State=inbox; Intent=capture; Topic=<topic>_execution_discovery`.
- Use `.workflow/tasks/build.md` as the task contract.

Request:
${input:request:optional build constraints, target segment, or verification notes}

Return:
- Execution Trace
- Execution Environment Contract
- Changed files, when full output is needed
- Verification Trace for each major step, when full output is needed
- Pitfalls Encountered, when any
- Reusable Execution Discoveries, when any
- Suggested Persist Candidate, only when worth saving
- Stop reason, if blocked
