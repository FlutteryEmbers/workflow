# Plan

{{CONTENT: /.workflow/templates/_persist_metadata.md}}

## Language / Style

{{default: Chinese explanations with English technical terms preserved; use full English only when requested}}

## Plan Summary

- Target Outcome: {{what should be true after execution}}
- Recommended Path: {{short sequence, staged path, or work package summary}}
- Input Sufficiency: {{insufficient | sufficient-for-draft | sufficient-for-handoff}}
- Input Gaps: {{only when insufficient; missing input categories, or omitted}}
- Main Risk: {{main risk or none}}

## Source Basis

### Shape Summary

Required. Use `Source: chat` when there is no persisted shape artifact.

- Source: {{chat | shape artifact | inbox brief | project docs | user request}}
- Motivation: {{one sentence explaining why this direction matters; use unknown when unavailable}}
- Selected Direction: {{one-line chosen direction}}
- Locked Decisions: {{confirmed choices that affect scope, sequence, compatibility, or verification}}
- Assumptions: {{defaults or inferred choices and risk if wrong}}

### Evidence / Repo Basis

- Relevant Files: {{files, packages, docs, or none}}
- Source Facts: {{facts that affect sequence, scope, verification, or target files}}
- Constraints Found: {{repo, docs, compatibility, architecture, or process constraints}}

### Open Basis

- Unknowns: {{unknown facts or unresolved inputs}}
- Non-blocking Follow-up Questions: {{none | future considerations}}

## Impact Surface

- Scope Size: {{small | medium | large}}
- Affected Surfaces: {{workflow core | task docs | templates | adapters | project docs | source code | tests | other}}
- Risk: {{low | medium | high}}
- Reversal Cost: {{low | medium | high}}
- Docs / Sync Impact: {{none | suggested | required}}

### Plan At A Glance

3-7 surface-level summary changes for scanability. This is not a step list, verification plan, full diff, or replacement for the plan.

| Change | Target | Reason | Risk |
| :--- | :--- | :--- | :--- |
| {{change}} | {{file/module/doc/behavior}} | {{why}} | {{low/medium/high}} |

## Plan

{{work packages, phases, or sequencing with scope, constraints, minimum viable verification, fallback verification, and stop conditions. Use bullets for small plans and a table for larger handoffs. Omit when Input Sufficiency is insufficient.}}

## Scope

- Allowed Changes: {{files, docs, behavior, or interfaces allowed to change}}
- Do Not Touch: {{path, behavior, interface, data, or docs area}}
- Out Of Scope: {{work explicitly excluded, or none}}

## Verification

- Success Criteria: {{what must be true when this plan is done}}
- Minimum Viable Verification: {{targeted existing checks, fixture/unit/static/smoke/manual validation, or none}}
- Verification Feasibility: {{available | partial | unavailable | unknown}}
- Fallback Verification: {{fallback checks when stronger verification is unavailable, or none}}
- Residual Risk: {{risk left after minimum/fallback verification, or none}}
- Higher Assurance: {{only when Lens: test or explicitly requested; contract/baseline/parity/regression strategy, or none}}
- Checks: {{tests, commands, review checks, or manual validation}}
- Acceptance Evidence: {{what evidence proves the plan worked}}

## Stop Conditions

- {{condition that requires stopping instead of expanding scope}}

## Compatibility / Constraint Policy

- Compatibility: {{preserve | breaking}}
- Constraint Mode: {{respect | propose_override | prototype_exception}}
- Removed Compatibility: {{old paths, aliases, behavior, schema, prompts, or none}}
- Migration / Alias: {{kept | removed | none | explicitly not provided}}
- Constraint Exceptions: {{constraint and reason, or none}}
- Do Not Preserve: {{legacy behavior intentionally dropped, or none}}
- Cleanup Required: {{old files, docs, prompts, tests, or none}}

## Risk / Recovery

- Risks / Unknowns: {{execution risk or missing information}}
- Rollback / Recovery: {{how to revert, recover, or stop safely if this plan fails}}
- Handoff Notes: {{context an implementer or external agent needs to use this plan without making product decisions}}

## Review / Next Use

- Recommended Next Task: {{shape | explore | review | plan | persist | sync | build | external-agent | none}}
- Next Use: {{review | persist | build with explicit invocation | external-agent | sync | none}}
- Stable Document Follow-up: {{none | sync project-docs | sync session-archive; target and reason if known}}

## Notes To Preserve

{{preserve discussion details that help future readers understand, revise, implement, or audit this plan. Include user corrections, preferences, examples, constraints, why the sequence changed, accepted risks, or weak-model handoff details. Do not preserve full transcript or conversational noise.}}
