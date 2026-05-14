---
id: review
role: reviewer
purpose: Review behavior, evidence, risks, defects, or refactor opportunities.
inputs:
  - target_or_claim
outputs:
  - .docs/work/reviews/review_{topic}.md
  - .docs/changes/{change_id}/evidence.md
optional_lenses:
  - debug
  - test
  - architecture
  - knowledge
  - change
done_check:
  - findings_are_actionable
  - severity_or_confidence_is_clear
  - evidence_is_recorded
---

# Review Task

## Context Injection

Role: {{CONTENT: /workflow_core/roles/reviewer.md}}
Template: {{CONTENT: /workflow_core/templates/review.md}}

## Instructions

Inspect the target and report findings first. Keep review scope explicit and avoid rewriting the solution unless the user asks for a fix plan.

## Optional Lens Escalation

- Use {{CONTENT: /workflow_core/lenses/debug.md}} for bugs or uncertain behavior.
- Use {{CONTENT: /workflow_core/lenses/test.md}} for verification gaps.
- Use {{CONTENT: /workflow_core/lenses/architecture.md}} for structural risks.
- Use {{CONTENT: /workflow_core/lenses/knowledge.md}} for docs or knowledge drift.
- Use {{CONTENT: /workflow_core/lenses/change.md}} when evidence belongs to a tracked change.

## Output Rules

- Default path: `{WorkflowRoot}/.docs/work/reviews/review_{topic}.md`
- With `change` lens: `{WorkflowRoot}/.docs/changes/{change_id}/evidence.md`

## User Input

{{code, docs, behavior, claim, or risk to review}}
