---
id: plan
role: designer
purpose: Turn a chosen direction into a repo-aware plan or external-agent handoff in chat.
inputs:
  - decision_or_target
outputs:
  - chat_plan
  - persist_packet
user_selectable_lenses:
  - iteration
  - expand
  - language
  - strategy
  - test
  - architecture
done_check:
  - sequence_is_executable
  - constraints_are_named
  - verification_is_defined
---

# Plan Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- Do not load templates and do not write files.
- If the user asks to persist or provides a target, return `Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task; use `build` with an approved plan.
- For native Plan/Implement, use the external-agent path.

## When To Use

- Use when the target direction is chosen and the user needs repo-aware implementation steps, sequencing, or external-agent handoff.
- Use when a plan must name success criteria, allowed changes, do-not-touch areas, verification, and stop conditions before any write.

## Do Not Use When

- Do not use to invent the target direction; use `shape`.
- Do not use to judge whether a plan, target, code, or diff is good; use `review`.
- Do not use to implement the plan; use `build` or the external-agent path after review.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.

## Expected Output

- A repo-aware plan with `Success Criteria`, `Allowed Changes`, `Do Not Touch`, and step-level `Verify`.
- `Persist Packet` when the plan should become a draft handoff or accepted plan.

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
- relevant `.session/goal/*`, `.session/inbox/**`, `.session/drafts/**`, `.session/accepted/**`, docs, and target source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest repo-aware plan or handoff another engineer, Codex, Copilot, OpenCode, or similar agent can execute without making product decisions. Include target files, success criteria, allowed changes, do-not-touch areas, step-level verification, rollback or recovery notes, stop conditions, and target docs affected when they matter.

Every major step must use `Step / Change / Verify / Risk / Stop Condition`. If the verification method is unclear, mark the plan as not ready and recommend `review` or `shape` instead of treating it as approved.

Include `Docs Follow-up` only when the planned change clearly affects architecture, public behavior, module responsibility, execution constraints, or agent/human onboarding context. Do not force docs impact analysis for small or temporary changes.

If the plan depends on unverified assumptions, touches high-risk boundaries, or will enter `build` / external-agent Implement, recommend plan audit with `review --lens redteam,test`. This is a suggestion only; do not load the `redteam` lens unless the user explicitly selected it.

## Persist Packet

When useful, end with:

```text
Persist Packet:
Artifact: plan
Status: draft | accepted
Intent: handoff | decision | audit
Depth: detailed
Topic: <topic>
Suggested Target: .session/drafts/plan_<topic>.md or .session/accepted/plan_<topic>.md
Source Context:
- <accepted decision, target design, code/docs evidence, or planning discussion>
Target Outcome:
- <what should be true after execution>
Key Points:
- <sequence summary and main constraints>
Decision-Relevant Facts:
- <facts affecting execution order or scope>
Reasoning Trail:
- <why this sequence is preferred>
Allowed Changes:
- <paths, behavior, docs, tests, or prompts allowed to change>
Do Not Touch:
- <areas excluded from the plan>
Step -> Verify:
- <step> -> <verification>
Stop Conditions:
- <when to stop and return to plan/review>
Risks / Unknowns:
- <execution risk or missing information>
Examples / Pseudocode:
- <implementation sketch if useful>
Handoff Notes:
- <minimal context for build or external-agent>
Docs Follow-up:
- <none | suggested | required; include target and reason only when future human/agent execution could be misled>
Next Use:
- <persist draft | review | persist accepted | build | external-agent>
```

Accepted plans require explicit accepted, approved, or promote intent.
If the plan is not worth preserving, output `Persist Packet: none`.

## User Input

{{decision, target design, repo context, or implementation planning request}}
