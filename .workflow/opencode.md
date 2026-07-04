# OpenCode Low-Model Adapter

OpenCode support is a low-model external-agent adapter. It is not a second
Workflow Lite implementation and it does not expose the full task, lens,
template, persist, sync, or routing protocol.

Use this adapter when OpenCode is backed by a fast or weaker model that works
better with short, concrete instructions than with the complete Workflow Lite
protocol. Stronger agents can still use the main Workflow Lite flow through
Codex or Copilot.

## Operating Model

OpenCode has four allowed jobs:

- `context`: read relevant files and summarize current state.
- `plan`: draft a narrow external implementation plan from a chosen goal.
- `implement`: execute only an explicit plan with bounded scope.
- `review`: review a plan or diff and report blocking/non-blocking gaps.

OpenCode must not run Workflow Lite task routing. It must not apply lenses,
load templates, emit persist candidates, write `.session/**`, or perform stable
document sync. Session artifacts and stable docs remain owned by the main
Workflow Lite protocol.

## Handoff Packet

Use this packet to connect stronger-model workflow discussion to OpenCode work:

```text
Goal:
Source Context:
Locked Decisions:
Open Questions:
Allowed Changes:
Do Not Touch:
Verification:
Next Action:
```

Keep packets short. OpenCode should treat missing packet fields as missing
prerequisites, not as permission to infer workflow decisions.

## Commands

OpenCode project commands live in `.opencode/commands/**`.

- `/oc-context`: read-only context summary.
- `/oc-plan`: external implementation plan draft.
- `/oc-implement`: bounded implementation from an explicit plan.
- `/oc-review`: read-only plan or diff review.

OpenCode compatibility commands for the old Workflow Lite shortcut surface are
intentionally absent. Workflow Lite task commands remain available through
Copilot prompts and Codex shortcuts where those paths are stronger.

## Context

Use `/oc-context` when the model should only gather facts.

Rules:

- Do not edit files.
- Do not create an implementation plan.
- Prefer concrete file paths and observed facts over workflow terminology.
- Return current state, relevant files, missing evidence, and a next action
  packet.

## Plan

Use `/oc-plan` only after the direction is chosen.

The plan must include:

- target outcome
- allowed changes
- do-not-touch areas
- verification
- stop conditions

The plan is an external draft. It does not authorize edits, session writes, or
docs sync by itself.

## Implement

Use `/oc-implement` only with an explicit plan in the prompt.

Before editing, the plan must name:

- target outcome
- allowed changes
- do-not-touch areas
- verification
- stop conditions

If any of those are missing, OpenCode must stop and report the missing
prerequisite. It must not edit `.session/**`, `.workflow/**`, `docs/**`,
`.github/**`, or unrelated files unless the explicit plan lists them.

Implementation should be minimal and bounded. Stop instead of expanding scope,
removing unplanned compatibility, bypassing constraints, or inventing new
architecture.

## Review

Use `/oc-review` for plan review or diff review.

Review output starts with:

```text
Review Verdict: ready | needs changes | needs more evidence | blocked
```

Then list:

- blocking gaps
- non-blocking gaps
- evidence checked
- recommended next action

Review must not rewrite the full solution, sync docs, or create session
artifacts.

## Stable Documents And Session Memory

OpenCode does not own:

- `persist` to `.session/inbox/**` or `.session/threads/**`
- `sync` to `docs/**`, `src/**/README.md`, or `.session/archive/**`
- Workflow Lite task/lens/template maintenance

Use the main Workflow Lite flow through Codex or Copilot for those operations.
