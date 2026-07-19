---
id: distill
role: summarizer
purpose: Generate a user-directed summary or distillation of specified source material without judging, planning, writing, or projecting stable documents.
inputs:
  - source
  - summary_focus
  - summary_type
  - audience
  - next_use
outputs:
  - chat_distillation
  - persist_hint
user_selectable_lenses:
  - language
done_check:
  - source_scope_is_named
  - summary_focus_is_named
  - observed_inferred_unknown_are_separated
---

# Distill Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/summarizer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must state what the user wants summarized or distilled.
- `Mode: discuss` is default and is the only valid mode for this task.
- Do not load templates and do not write files.
- If the user asks to persist or provides a target, return a `Persist Candidate` and route the write to `persist`; do not construct an intermediate packet.
- `Mode: persist` is not valid for this task; use `persist` for `.session/**` or explicit `notes/**`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user asks to summarize, distill, compress, or extract structure from specified source material.
- Use for summaries of a file, folder, document set, thread, chat material, code area, or reference material when the user names the content or summary focus.
- Use for `archive-summary-draft` when the user wants a draft summary before `sync session-archive`.
- Use when the next task needs a compact source briefing, structured summary, or reusable distillation before `persist`, `shape`, `review`, `plan`, or `sync`.

## Do Not Use When

- Do not discover what exists, where it is, how it works, or how reliable evidence is; use `explore`.
- Do not judge correctness, readiness, source of truth, or whether the summary is accurate; use `review`.
- Do not decide direction, architecture, or artifact ownership; use `shape`.
- Do not create implementation steps; use `plan`.
- Do not write `.session/**`, `notes/**`, `docs/**`, `.session/archive/**`, or source files; use `persist`, `sync`, or `build`.

## Boundary Layers

- `Core Responsibility`: summarize or distill the user-selected source and focus while separating observed facts, inferences, and unknowns.
- `Adjacent Allowance`: include next use, persist candidate, review suggestion, source-scope caveat, or sync/archive handoff hint when they help the user use the summary.
- `Forbidden Authority`: do not discover broad source scope, judge accuracy, decide source of truth, choose direction, create implementation sequencing, stable-sync documents, write files, execute, or implement.

Adjacent allowance must stay secondary to the distillation. If the user primarily wants accuracy judgment, source-of-truth decision, or archive writing, route to `review` or `sync`.

## Result Requirements

- A user-directed summary with `Source`, `Summary Focus`, `Summary Type`, `Observed`, `Inferred`, `Unknown`, and `Next Use`.
- `Recommended Next Task` when the summary naturally leads to `persist`, `review`, `shape`, `plan`, or `sync`.
- One standard chat response using the shared response groups below.
- `Persist Candidate` only when the distillation is worth preserving.

## Task Boundary Check

Before distilling, classify the request:

- `fits`: user asks to summarize, distill, compress, or extract structure from specified sources.
- `fits_with_preflight`: source scope is broad or ambiguous, but read-only source inspection can identify a safe bounded summary scope.
- `composite`: user asks to distill and persist, review, sync, shape, or plan; distill first, then route to the next task.
- `wrong_task`: user asks what exists, where it is, or how it works; recommend `explore`.
- `wrong_task`: user asks whether a source, claim, summary, or draft is correct, ready, or source of truth; recommend `review`.
- `wrong_task`: user asks to archive a completed thread directly; recommend `sync session-archive`.
- `missing_prerequisite`: source scope or summary focus is missing and cannot be inferred safely.

Boundary handling:

- `fits`: summarize in chat.
- `fits_with_preflight`: inspect only enough source structure to bound the summary, then distill or return missing scope.
- `composite`: output the distillation plus the next-task prompt; do not write files.
- `wrong_task` or `missing_prerequisite`: output `Boundary Advice`. Still provide a bounded summary when a safe source/focus exists; return boundary-only output only when no selected source can be summarized or the request requires write, sync, execution, or judgment authority.

## Summary Types

- `structure-summary`: summarize organization, sections, modules, or information architecture.
- `folder-summary`: summarize files or directories and their responsibilities.
- `content-summary`: summarize substantive content without changing source-of-truth status.
- `decision-summary`: summarize decisions, assumptions, rejected options, and open questions.
- `interface-summary`: summarize public surfaces, inputs, outputs, dependencies, or contracts.
- `risk-summary`: summarize risks, weak evidence, conflicts, or unknowns without issuing a verdict.
- `archive-summary-draft`: summarize a thread for possible later `sync session-archive`; this is not the archive write.

## Copilot Add Context

Required:

- #.workflow/tasks/distill.md
- source files, folders, docs, discussion, or `.session/threads/**` material to summarize

User-selected lenses:

- Add #.workflow/lenses/language.md only if the user selects `language`.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Distill only the user-selected source and focus. Keep source boundaries explicit. Separate directly observed facts from interpretation and unknowns. If the source is too broad for a reliable summary, narrow by source scope, summary focus, or next use instead of producing a vague summary.

`distill` may generate an `archive-summary-draft`, but it must not write `.session/archive/**`. `sync session-archive` may still generate a final archive summary inline when archive prerequisites are complete; distill is not a hard prerequisite for archive sync.

Do not decide whether the summary is correct or stable project truth. Recommend `review` when accuracy, readiness, source-of-truth, or conflict resolution matters. Recommend `persist` only to save the distillation as `Artifact: distillation`.

## Response Contract

Use one standard response. Keep the shared groups in order, preserve the
task-specific field names, and omit optional groups that have no content:

```text
User Intent
- <one line about what the user wants summarized>
Task State
- Boundary Advice: <Boundary, Why, Useful Response Now, Advisory Next Task; omit when it adds no value>
- Source Boundaries: <source files, folder, thread, docs, or discussion>
- Summary Focus: <focus and audience>
- Summary Type: <structure-summary|folder-summary|content-summary|decision-summary|interface-summary|risk-summary|archive-summary-draft>
Primary Result
- Summary / Structured Summary: <summary grouped for the requested next use>
- Observed: <source-backed facts>
- Inferred: <interpretations with source basis, or none>
- Unknown: <missing source, weak evidence, or unresolved ambiguity>
Supporting Information
- Omitted / Out Of Scope: <optional excluded sources or dimensions>
Next
- Next Use: <persist|review|shape|plan|sync|none>
- Recommended Next Task: <persist|review|shape|plan|sync|none>
Persistence
- Persist Candidate: Artifact=distillation; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

Omit `Persistence` when the distillation is not worth preserving. A request for
more detail expands these same fields; it does not select another response mode
or load an artifact template.

## User Input

{{source, summary focus, summary type, audience, next use, and distill request}}
