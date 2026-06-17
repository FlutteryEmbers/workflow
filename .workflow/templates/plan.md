# Plan

## Persist Metadata

- Artifact: plan
- Topic: {{topic}}
- Artifact State: {{working | settled | superseded}}
- Thread: {{thread-name}}
- Intent: {{handoff | decision | audit}}
- Depth: {{detailed}}
- Source: {{recent discussion | existing artifact | file path}}
- Target: {{.session/...}}
- Last Updated: {{date}}

## Language / Style

{{default: Chinese explanations with English technical terms preserved; use full English only when requested}}

## Decision Link

- Thread plan: `.session/threads/<thread>/plan_<topic>.md`

## Plan Summary

- Target Outcome: {{what should be true after execution}}
- Recommended Path: {{short sequence, staged path, or work package summary}}
- Plan Readiness: {{incomplete | reviewable | execution-candidate}}
- Readiness Rationale: {{why this readiness applies}}
- Next Action: {{shape | explore | review | plan | build | external-agent | sync | persist | none}}
- Main Risk: {{main risk or none}}
- Source Basis: {{chat | shape artifact | inbox brief | decision | project docs}}

## Shape Summary

- Source: {{chat | shape artifact | inbox brief | decision | project docs}}
- Selected Direction: {{one-line chosen direction}}
- Key Decisions: {{decisions that affect execution order, scope, compatibility, or verification}}
- Assumptions: {{defaults or inferred decisions and risk if wrong}}

## Impact Surface

- Scope Size: {{small | medium | large}}
- Affected Surfaces: {{workflow core | task docs | templates | adapters | project docs | source code | tests | other}}
- Risk: {{low | medium | high}}
- Reversal Cost: {{low | medium | high}}
- Docs / Sync Impact: {{none | suggested | required}}

## Planning Basis

- Source Direction: {{shape artifact, decision, user request, project doc, or inferred target}}
- Locked Decisions: {{confirmed decisions and sources}}
- Assumed Decisions: {{recommended defaults and risk if wrong}}
- Rejected Options: {{options rejected because they affect sequence, scope, or constraints; none if not relevant}}
- Known Gaps: {{none | missing or weak planning input}}

## Known Gaps

- {{none | missing direction, evidence, target, verification, source-of-truth, compatibility, or scope input}}

## Review Focus

- {{what review should inspect before build, sync, or handoff}}

## Discussion Notes To Preserve

{{preserve any discussion detail that would help future readers understand, revise, implement, or audit this plan. This may include user corrections, user preferences, examples, counterexamples, constraints, why the sequence changed, accepted risks, or details a weaker model might otherwise miss. Do not preserve full transcript or conversational noise.}}

## Source Context

- {{thread decision, shape artifact, inbox brief, project doc, code path, or user correction}}

## Target Outcome

{{what should be true after execution}}

## Plan

{{steps, phases, or work packages with scope, constraints, verification, and stop conditions}}

## Decision-Relevant Facts

- {{fact that affects sequence, scope, verification, or target files}}

## Assumptions vs Facts

- Fact: {{confirmed input}}
- Assumption: {{inference that still needs validation}}

## Planning Rationale

- Why This Sequence: {{reason}}
- Rejected Sequencing: {{alternatives and why not}}
- Follow-up Questions: {{none | non-blocking future considerations}}

## Success Criteria

- {{what must be true when this plan is done}}

## Allowed Changes

- {{files, docs, behavior, or interfaces allowed to change}}

## Do Not Touch

- {{path, behavior, interface, data, or docs area}}

## Compatibility / Constraint Plan

- Compatibility: {{preserve | breaking}}
- Constraint Mode: {{respect | propose_override | prototype_exception}}
- Removed Compatibility: {{old paths, aliases, behavior, schema, prompts, or none}}
- Migration / Alias: {{kept | removed | none | explicitly not provided}}
- Constraint Exceptions: {{constraint and reason, or none}}
- Do Not Preserve: {{legacy behavior intentionally dropped, or none}}
- Cleanup Required: {{old files, docs, prompts, tests, or none}}
- Stop Conditions: {{when breaking scope or exceptions exceed the explicit plan}}

> Default to `Compatibility: preserve` and `Constraint Mode: respect` unless the user or explicit source chooses otherwise.

## Current Repo Fit

- Relevant Files: {{files, packages, docs, or none}}
- Reusable Parts: {{what can be reused}}
- Conflicts: {{where current repo shape conflicts with target direction}}

## Impact Map

| Target | Files / Docs | Change | Risk |
| :--- | :--- | :--- | :--- |
| {{target}} | {{paths}} | {{add/change/remove}} | {{risk}} |

## Detailed Step Sequence

| Step | Change | Verify | Risk | Stop Condition |
| :--- | :--- | :--- | :--- | :--- |
| {{step}} | {{change}} | {{test, check, or manual verification}} | {{risk}} | {{when to stop and return to plan/review}} |

## Verification

- {{test, check, or manual verification}}

## Stop Conditions

- {{condition that requires stopping instead of expanding scope}}

## Risks / Unknowns

- {{execution risk or missing information}}

## Rollback / Recovery

- {{how to revert or recover if this plan fails}}

## Handoff Contract

{{success criteria, allowed changes, do-not-touch areas, verification, minimal diff constraints, and stop conditions for native Plan/Implement, if relevant}}

## Handoff Notes

- {{context an implementer or external agent needs to execute without making product decisions}}

## Target Docs

- {{docs path or none}}

## Stable Document Follow-up

{{include only when the plan clearly affects architecture, public behavior, module responsibility, execution constraints, agent/human onboarding context, or thread closure}}

- Impact: {{none | suggested | required}}
- Sync Domain: {{project-docs | session-archive | none}}
- Target: {{allowed docs/** path, src/**/README.md, .session/archive/<thread>/summary.md, or none}}
- Reason: {{what alignment or retrieval mistake could happen without sync}}
- Suggested Sync: {{sync prompt or none}}

## Project Docs Conditions

{{required only when the plan allows direct docs/** edits; otherwise use sync}}

- Source: {{source material}}
- Alignment Purpose: {{what code/docs alignment mistake this doc should prevent}}
- Source Of Truth: {{confirmed source}}
- Alignment Success Criteria: {{what must remain aligned after sync}}
- Existing Docs Structure: {{preserve or describe intended change}}
- Safety: {{session-only residue, temporary PoC detail, low-level mirror content, and misleading details removed}}

## Recommended Next Task

{{shape | explore | review | plan | persist | sync | build | external-agent | none}}

## Next Use

{{review, persist, build, external-agent, sync, or none}}
