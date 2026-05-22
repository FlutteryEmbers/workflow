---
description: Use Workflow Lite with explicit user-selected lenses.
argument-hint: "Mode=<discuss|persist|execute>; Write Path=<workflow-managed|external-agent>; Task=<route|clarify|explore|shape|plan|build|review|sync>; Lens=<none|iteration|expand|consistency|distill|language|domain|strategy|redteam|test|architecture|debug>; Target=<required for persist>; Plan=<required for execute>; Request=<what you want>"
---

# Workflow Lite Prompt

Use Workflow Lite with progressive context.

## Required Input

```text
Mode: ${input:mode:discuss}
Write Path: ${input:write_path:workflow-managed|external-agent}
Task: ${input:task:route|clarify|explore|shape|plan|build|review|sync}
Lens: ${input:lens:none; comma-separated lenses allowed only when explicitly selected}
Target: ${input:target:required for persist; otherwise none}
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
- Read-only preflight is allowed only in `Mode: discuss`; do not load templates, write files, run implementation, or apply unselected deep lenses during preflight.
- Implicit preflight defaults to `shape`, `plan`, and `sync`; conditional preflight applies to `review`, `build`, and `explore`; no implicit preflight runs for `clarify` or `route`.
- Ask for confirmation only when ambiguity would affect file writes, execution, source of truth, or material scope.
- Use `Lens: none` unless the user explicitly names or adds lens files.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed; follow the user's lens order.
- Do not infer, auto-apply, or load all lenses.
- In `Mode: discuss`, do not load templates and do not create or update files.
- In ordinary `Mode: persist`, load the matching template and write only the requested `.session/**` target.
- `Task: sync` in `Mode: persist` may write only `docs/**` or explicit `src/**/README.md` targets.
- In `Mode: execute`, require `Task: build` and an approved plan.
- If using Codex/Copilot native Plan -> Implement, set `Write Path: external-agent`; external-agent is not a Mode.
- For `Write Path: external-agent`, audit the native plan before implementation and review the diff after implementation.
- Block instead of writing when `Mode: persist` lacks `Target`, `Mode: execute` lacks `Plan`, the target is outside the mode boundary, or instructions conflict.
- `docs/**` writes must follow Formal Docs Rules.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.

## Formal Docs Rules

- Source, target audience, source of truth, and reader-facing success criteria must be clear.
- Preserve existing docs tone and structure when updating.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, internal redteam-only risks, temporary workarounds, sensitive implementation detail, and not-yet-announced plans.
- If source, audience, source of truth, reader-facing success criteria, or safety is unclear, output `docs blocked` and do not write `docs/**`.

## Context Format

Add the selected task file from `.workflow/tasks/`.
Add the matching template from `.workflow/templates/` only in `Mode: persist`.
Add selected lens files from `.workflow/lenses/` only when `Lens` is not `none`.
Add relevant `.session/goal/*`, `.session/notes/**`, `.session/decisions/**`, `docs/**`, and source files.

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
Audit this external plan before native implementation. Return approved, needs changes, blocked, or docs blocked.
```

Diff review:

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the diff against the approved external plan. Identify scope drift, missing verification, and follow-up.
```
