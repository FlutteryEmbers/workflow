---
description: Use Workflow Lite with explicit user-selected lenses.
argument-hint: "Mode=<discuss|persist|execute>; Write Path=<workflow-managed|external-agent>; Task=<route|clarify|explore|shape|plan|persist|build|review|sync>; Lens=<none|iteration|expand|consistency|distill|language|domain|strategy|redteam|test|architecture|debug>; Intent=<summary|exploration|decision|audit|handoff|constraint|reference>; Depth=<compact|standard|detailed>; Thread=<thread-name>; Target=<required for sync/docs/code; optional for persist>; Plan=<required for execute>; Request=<what you want>"
---

# Workflow Lite Prompt

Use Workflow Lite with progressive context.

## Required Input

```text
Mode: ${input:mode:discuss}
Write Path: ${input:write_path:workflow-managed|external-agent}
Task: ${input:task:route|clarify|explore|shape|plan|persist|build|review|sync}
Lens: ${input:lens:none; comma-separated lenses allowed only when explicitly selected}
Intent: ${input:intent:required for persist; otherwise none}
Depth: ${input:depth:compact|standard|detailed; optional for persist}
Target: ${input:target:required for sync/docs/code; optional for persist; otherwise none}
Thread: ${input:thread:optional for persist thread target inference}
Plan: ${input:plan:required for execute; otherwise none}
Request: ${input:request:describe the work}
```

## Rules

- Use exactly one task as the main workflow context.
- Default to `Mode: discuss`.
- Start with an inline `Understanding Check` unless the request is trivial.
- Run a lightweight Task Boundary Check before acting.
- Classify boundary as `fits`, `fits_with_preflight`, `composite`, `wrong_task`, or `missing_prerequisite` when the request is not straightforward.
- If composite, output segmented prompts with stop points instead of forcing the request into one task.
- When unsure, start with `shape`.
- Ambiguous what-if, strategy, conceptual, direction-setting, or entrypoint-selection requests default to `shape`.
- Evidence-only requests go to `explore`; verdict-only requests go to `review`.
- Lenses may strengthen the selected task, but must not change task responsibility.
- Read-only preflight is allowed only in `Mode: discuss`; do not load templates, write files, run implementation, or apply unselected deep lenses during preflight.
- Implicit preflight defaults to `shape`, `plan`, and `sync`; conditional preflight applies to `review`, `build`, and `explore`; no implicit preflight runs for `clarify` or `route`.
- Ask for confirmation only when ambiguity would affect file writes, execution, source of truth, or material scope.
- Use `Lens: none` unless the user explicitly names or adds lens files.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed; follow the user's lens order.
- Do not infer, auto-apply, or load all lenses.
- In `Mode: discuss`, do not load templates and do not create or update files.
- Discussion tasks should produce `Persist Packet` when the result is worth preserving; output `Persist Packet: none` when it is not.
- In `Mode: persist`, use `Task: persist` for `.session/**` artifacts or `Task: sync` for `docs/**` / `src/**/README.md`.
- For `persist`, `.session/inbox/**` and `.session/threads/{thread}/{artifact}_{topic}.md` targets may be inferred from `Artifact + Thread + Topic`.
- For `persist`, explicit `notes/**` targets may be written as disposable exploration memory; never infer `notes/**`.
- Use `persist shape_<topic>` to reference a shape by `Artifact ID`; in multi-topic discussion, persist the main goal over the latest topic unless explicitly targeted.
- `notes/**` is not project docs and is not an execution source.
- For `persist`, preserve decision-relevant reasoning, not full transcript. Use `Depth: detailed` for shape, option, plan, review, decision, distillation, and expanded artifacts unless the user asks for compact output.
- `persist` may apply explicit review edits, but must not choose a new direction, re-plan execution, or judge whether review feedback is correct.
- `Task: sync` in `Mode: persist` may write only `docs/**` or explicit `src/**/README.md` targets.
- In `Mode: execute`, require `Task: build` and an explicit executable plan.
- If using Codex/Copilot native Plan -> Implement, set `Write Path: external-agent`; external-agent is not a Mode.
- For `Write Path: external-agent`, audit the native plan before implementation and review the diff after implementation.
- Block instead of writing when `Mode: execute` lacks `Plan`, the target is outside the mode boundary, or instructions conflict.
- `docs/**` writes must follow Project Docs Rules.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`.
- `Compatibility: breaking` or `Constraint Mode: propose_override | prototype_exception` require explicit user or explicit-source intent.
- `shape` may suggest breaking/override pressure; `plan` must encode selected policy; `build` must stop on unplanned compatibility removal or constraint override.

## Project Docs Rules

- Source, future use, source of truth, and future-use success criteria must be clear.
- Preserve existing docs tone and structure when updating.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, temporary PoC detail, low-level implementation mirror content, and details that would mislead future execution.
- If source, future use, source of truth, future-use success criteria, or safety is unclear, output `docs blocked` and do not write `docs/**`.
- `docs/**` is updated only when drift would cause future human/agent execution mistakes.

## Context Format

Add the selected task file from `.workflow/tasks/`.
Add the matching template from `.workflow/templates/` only for `persist` or `sync` in `Mode: persist`.
For new `docs/**` targets, add `project_doc.md` or `architecture_note.md`; for existing docs, preserve the target structure.
Add selected lens files from `.workflow/lenses/` only when `Lens` is not `none`.
Add relevant `.session/goal/*`, `.session/inbox/**`, `.session/threads/**`, `docs/**`, and source files.

## Boundary Output

```text
Boundary: <fits|fits_with_preflight|composite|wrong_task|missing_prerequisite>
Reason: <one sentence>
Recommended Path: <task -> task>
Next Prompt: <copyable prompt>
```

For composite requests, provide:

```text
Recommended Segments:
1. <segment name>
   Mode:
   Task:
   Lens:
   Target/Plan:
   Context:
   Request:
   Expected Output:
   Continue Condition:
Stop Points:
- <where approval, audit, or source-of-truth confirmation is required>
```

## External-Agent Review Formats

Plan audit:

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this external plan before native implementation. Return ready, needs changes, blocked, or docs blocked.
```

Diff review:

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the diff against the explicit external plan. Identify scope drift, missing verification, and follow-up.
```
