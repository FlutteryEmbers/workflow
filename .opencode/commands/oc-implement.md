---
description: OpenCode bounded implementation from an explicit plan.
---

Implement only the explicit plan supplied in the prompt.

Required plan fields:
- Target outcome
- Allowed changes
- Do not touch
- Verification
- Stop conditions

Rules:
- If any required plan field is missing, stop and report the missing prerequisite.
- Do not infer permission from surrounding discussion.
- Do not broaden scope.
- Do not perform drive-by refactors, formatting churn, or unrelated cleanup.
- Do not edit `.session/**`, `.workflow/**`, `docs/**`, `.github/**`, or unrelated files unless the plan explicitly lists them.
- Stop if implementation requires scope expansion, unplanned compatibility removal, or unplanned constraint bypass.
- Run only verification commands named by the plan or clearly provided by the repo.

Request:
$ARGUMENTS

Return:
- Result: completed | partial | blocked
- Changed files
- Verification
- Deviations from plan
- Stop reason, if blocked
- Recommended next action
