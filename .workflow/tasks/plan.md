---
id: plan
role: designer
purpose: Turn a chosen direction into a planning draft, repo-aware plan, or external-agent handoff in chat.
inputs:
  - decision_or_target
outputs:
  - chat_plan
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - architecture
  - test
  - language
done_check:
  - sequence_is_executable
  - constraints_are_named
  - verification_is_defined
---

# Plan Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, requests a handoff, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task; use `build` with an explicit executable plan.
- For native Plan/Implement, use the external-agent path.

## When To Use

- Use when the target direction is chosen and the user needs repo-aware implementation steps, sequencing, or external-agent handoff.
- Use when a plan must name success criteria, allowed changes, do-not-touch areas, verification, and stop conditions before any write.

## Do Not Use When

- Do not use to invent the target direction; use `shape`.
- Do not use to judge whether a plan, target, code, or diff is good; use `review`.
- Do not use to implement the plan; use `build` or the external-agent path after review.
- Do not use to write session artifacts; use `persist`.
- Do not use to update stable documents; use `sync`.

## Expected Output

- A `Planning Draft` or implementation handoff appropriate to the user's requested level.
- Build-ready handoffs include `Success Criteria`, `Allowed Changes`, `Do Not Touch`, and step-level `Verify`.
- `Planning Basis` that records source shape/decision, locked decisions, assumed decisions, blocking decisions, and the chosen abstraction level.
- `Compatibility / Constraint Plan` that records the selected compatibility and constraint policy before execution.
- `Output: compact` default: short plan summary, critical risks, and optional `Persist Candidate`.
- `Full Persist Packet` only when the plan should be persisted now, used as a handoff, or `Output: full` is requested.

## Task Boundary Check

Before planning, classify the request:

- `fits`: target direction is chosen and the user needs repo-aware implementation steps or handoff.
- `fits_with_preflight`: plan depends on current code, project docs, session context, target-to-repo fit, target files, or verification readiness. In `Mode: discuss`, run default implicit preflight first.
- `composite`: user asks to plan and persist; plan first, then route to `persist`.
- `wrong_task`: target direction is not chosen; recommend `shape`.
- `wrong_task`: user asks whether current implementation or target is reasonable; recommend `review`.
- `composite`: user asks to implement from target docs and current code; recommend `plan -> review -> external-agent/build -> review`.

Default implicit preflight runs only in `Mode: discuss` and checks target stability, repo fit, target files, do-not-touch areas, and verification readiness. Plan may identify blockers and conflicts, but must not invent a new target. If the target is unstable, target and repo conflict, or verification is unclear, recommend `review` or `shape`.

## Copilot Add Context

Required:

- #.workflow/tasks/plan.md
- relevant `.session/inbox/**`, `.session/threads/**`, docs, and target source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest useful plan for the user's current intent. In `Mode: discuss`, plans may be non-build-ready planning drafts when the user is still exploring sequencing, phases, or options. Only implementation handoff or build-ready plans must include target files, success criteria, allowed changes, do-not-touch areas, step-level verification, rollback or recovery notes, stop conditions, and target docs affected when they matter.

For implementation handoff or build-ready planning, every major step must use `Step / Change / Verify / Risk / Stop Condition`. For planning drafts, use phases, work packages, dependencies, assumptions, risks, and what would be needed to turn the draft into an implementation handoff. If verification is unclear, mark the plan as a `Planning Draft` rather than treating it as ready for `build`.

## Abstraction Level Selection

Choose `Abstraction Level` automatically unless the user explicitly names one:

- If the user explicitly names `phase-plan` or `implementation-plan`, use that as the requested level, subject to readiness checks.
- If a source shape artifact includes `Recommended Next Abstraction Level`, inherit it by default.
- If no shape artifact is available, infer from the request and read-only preflight.
- Default to `phase-plan` when uncertain.

Use `phase-plan` for general planning, sequencing, staged work, roadmap-style requests, medium or large impact surfaces, medium or high reversal cost, medium or high execution risk, multiple affected surfaces, or any required user confirmation before implementation/build. `phase-plan` may include assumed decisions, but it is not direct build input.

Use `implementation-plan` only for build-ready planning, external-agent handoff, weak-model handoff, or explicit execution preparation. It must include success criteria, target areas or files, allowed changes, do-not-touch areas, step-level verification, stop conditions, and compatibility / constraint policy.

If `implementation-plan` is requested but readiness is incomplete, downgrade to `Abstraction Level: phase-plan` and include `What Would Make This Implementation-Ready`.

Block build-ready `implementation-plan` when any of these are unresolved:

- compatibility policy
- source of truth
- artifact boundaries
- target areas or files
- verification
- high-risk assumption
- source-of-truth, project-docs-truth, permissions, data, security, or architecture-constraint decision

Include stable-document follow-up only when the planned change clearly affects architecture, public behavior, module responsibility, execution constraints, agent/human onboarding context, or thread closure. Do not force docs/archive impact analysis for small or temporary changes.

If the plan depends on unverified assumptions, touches high-risk boundaries, or will enter `build` / external-agent Implement, recommend a critique review plus verification review. This is a suggestion only; do not load the `redteam` lens unless the user explicitly selected it.

When the user asks for a phase plan, implementation plan, build-ready handoff, or weak-model handoff, declare the current `Abstraction Level`:

- `phase-plan`: phases, work packages, dependencies, validation direction, sequencing options, and risks.
- `implementation-plan`: target files, allowed changes, do-not-touch areas, step -> verify, stop conditions, and handoff notes for weak-model or OpenCode execution.

Abstraction Level is core protocol, not a lens. Option comparison belongs in `shape`; `plan` organizes the selected direction into phases or executable handoff detail.

## Discussion Freedom

In `Mode: discuss`, `plan` may output a `Planning Draft` before all execution details are known.

- `Planning Draft`: phases, sequencing, dependencies, assumptions, risks, and open decisions; not build-ready.
- `Implementation Handoff`: target files, allowed changes, do-not-touch, step verification, stop conditions, and handoff notes.
- Include `Confidence`, `Assumptions`, `Human Decision State`, and `What Would Make This Implementation-Ready` when the plan is still a draft.
- Do not let a planning draft imply execution authorization.

## Compatibility / Constraint Policy

Default policy:

- `Compatibility: preserve`
- `Constraint Mode: respect`

`plan` must encode the selected policy into executable steps. It must not silently switch from `preserve` to `breaking`, remove migration/alias/fallback work, or introduce constraint exceptions unless the user explicitly requested them or the source decision already states them.

Use `Compatibility: breaking` only when explicitly requested by the user or explicit source. In that case, name removed compatibility, migration/alias decisions, cleanup, and stop conditions.

Use `Constraint Mode: propose_override` or `prototype_exception` only when explicitly requested by the user or explicit source. In that case, name the exception scope, reason, cleanup or review trigger, and whether it must stay out of long-term `docs/**` until confirmed.

If preserving compatibility makes the plan materially more complex, output a planning blocker or tradeoff instead of switching policy automatically.

Include:

```text
Compatibility / Constraint Plan
- Compatibility: preserve | breaking
- Constraint Mode: respect | propose_override | prototype_exception
- Removed Compatibility: <old paths, aliases, behavior, schema, prompts, or none>
- Migration / Alias: <kept | removed | none | explicitly not provided>
- Constraint Exceptions: <constraint and reason, or none>
- Do Not Preserve: <legacy behavior intentionally dropped, or none>
- Cleanup Required: <old files, docs, prompts, tests, or none>
- Stop Conditions: <when breaking scope or exceptions exceed the explicit plan>
```

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants planned>
Current Read: <optional one line about relevant plan/code/docs facts>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Abstraction Level: <phase-plan|implementation-plan>
Planning Draft: <yes/no; if yes, say what is missing for build-ready handoff>
Persist Candidate: Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

Use `Persist Candidate: none` when the plan is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to整理, refine, or prepare the plan for persist without writing files:

```text
User Intent: <one line about what the user wants planned>
Current Read: <optional one line about relevant plan/code/docs facts>
Refined Direction / Plan:
- <target outcome, abstraction level, phase plan, key constraints, and readiness>
Planning Basis:
- <source shape/decision, locked decisions, assumed decisions, blocking decisions>
What Would Make This Implementation-Ready:
- <target files, allowed changes, verification, stop conditions, or missing decision>
Discussion Notes To Preserve:
- <phase constraint, sequencing reason, user correction, accepted risk, example, or weak-model handoff detail>
Open Questions:
- <question that blocks readiness or execution>
Persist Candidate:
- Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs an implementation/external-agent handoff:

```text
Persist Packet:
Artifact: plan
Abstraction Level: <phase-plan | implementation-plan | none>
Artifact State: working | settled | superseded
Intent: handoff | decision | audit
Depth: detailed
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/plan_<topic>.md
Plan Snapshot:
- <target outcome, execution target, phase count, readiness, key constraint, key stop condition, next use>
Discussion Notes To Preserve:
- <discussion detail worth preserving because it affects understanding, revision, implementation, or audit>
Planning Basis:
- Source Direction: <shape artifact, decision, user request, project doc, or inferred target>
- Requested Abstraction Level: <phase-plan | implementation-plan | none>
- Selected Abstraction Level: <phase-plan | implementation-plan>
- Selection Reason: <shape recommendation | user request | inferred impact surface | readiness downgrade>
- Locked Decisions: <confirmed decisions and sources>
- Assumed Decisions: <recommended defaults and risk if wrong>
- Blocking Decisions: <unresolved decisions blocking implementation-plan, or none>
- Requires User Confirmation Before: <none | implementation-plan | build>
Source Context:
- <thread decision, target design, code/docs evidence, or planning discussion>
Target Outcome:
- <what should be true after execution>
Key Points:
- <sequence summary and main constraints>
Decision-Relevant Facts:
- <facts affecting execution order or scope>
Phase Plan:
- <phase> -> <goal, scope, allowed changes, constraints, verify, exit criteria, stop conditions>
Phase Constraints:
- <phase-level constraint or ordering rule that must survive persist>
Decision Trail:
- <why this sequence is preferred>
Allowed Changes:
- <paths, behavior, docs, tests, or prompts allowed to change>
Do Not Touch:
- <areas excluded from the plan>
Compatibility:
- <preserve | breaking>
Constraint Mode:
- <respect | propose_override | prototype_exception>
Removed Compatibility:
- <old path, alias, behavior, schema, prompt, or none>
Migration / Alias:
- <kept | removed | none | explicitly not provided>
Constraint Exceptions:
- <exception scope and reason, or none>
Do Not Preserve:
- <legacy behavior intentionally dropped, or none>
Cleanup Required:
- <old files, docs, prompts, tests, or none>
Step -> Verify:
- <step> -> <verification>
Stop Conditions:
- <when to stop and return to plan/review>
Risks / Unknowns:
- <execution risk or missing information>
What Would Make This Implementation-Ready:
- <missing target area, allowed change, verification, stop condition, or decision; none if already implementation-ready>
Preserve From Discussion:
- <phase boundaries, phase constraints, important examples/counterexamples, user corrections, accepted risks, or weak-model handoff details to preserve>
Examples / Pseudocode:
- <implementation sketch if useful>
Handoff Notes:
- <minimal context for build or external-agent>
Stable Document Follow-up:
- <none | project-docs | session-archive; include target, sync domain, and reason only when future alignment or retrieval could drift>
Next Use:
- <persist | review | build | external-agent>
```

If the plan is not worth preserving, output `Persist Candidate: none`.

## User Input

{{decision, target design, repo context, or implementation planning request}}
