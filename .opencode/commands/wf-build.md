---
description: Workflow Lite explicit-plan execution.
---

Use Workflow Lite build semantics.

Mode: execute
Output: full
Task: build
Lens: none unless explicitly requested

Rules:
- This command is an explicit execution entrypoint. User invocation authorizes execution of the provided plan only.
- Require `Plan:` as a file path, pasted explicit plan, or complete plan in the current prompt.
- Do not use `notes/**` as an execution plan source.
- If the plan is missing target files, allowed changes, do-not-touch areas, step verification, or stop conditions, return `missing_prerequisite` and do not edit files.
- Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.
- Do not modify `.session/**`, `.workflow/**`, `docs/**`, `.github/**`, or unrelated files unless the plan explicitly lists them.
- Stop if implementation requires scope expansion, unplanned compatibility removal, unplanned alias/migration/fallback removal, or unplanned constraint override.
- Use `.workflow/tasks/build.md` as the task contract if needed.

Request:
$ARGUMENTS

Return:
- Changed files
- Verification run for each major step
- Skipped items and why
- Stop reason, if blocked
