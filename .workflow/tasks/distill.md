---
id: distill
role: analyst
purpose: Generate a user-directed summary or distillation of specified source material without judging, planning, writing, or projecting stable documents.
inputs:
  - source
  - summary_focus
  - summary_type
  - depth
  - audience
  - next_use
outputs:
  - chat_distillation
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - language
done_check:
  - source_scope_is_named
  - summary_focus_is_named
  - observed_inferred_unknown_are_separated
---

# Distill Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/analyst.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must state what the user wants summarized or distilled.
- `Mode: discuss` is default and is the only valid mode for this task.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or requests `Output: full`, return `Full Persist Packet` and route the write to `persist`.
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

## Expected Output

- A user-directed summary with `Source`, `Summary Focus`, `Summary Type`, `Observed`, `Inferred`, `Unknown`, and `Next Use`.
- `Recommended Next Task` when the summary naturally leads to `persist`, `review`, `shape`, `plan`, or `sync`.
- `Output: compact` default: short summary and optional `Persist Candidate`.
- `Output: normal`: structured summary for review, shape, plan, sync, or persist.
- `Full Persist Packet` only when the distillation should be persisted now or `Output: full` is requested.

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
- `wrong_task` or `missing_prerequisite`: stop and return Boundary, Reason, Recommended Path, and Next Prompt.

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

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants summarized>
Source: <source files, folder, thread, docs, or discussion>
Summary Focus: <what dimension is being summarized>
Summary Type: <structure-summary|folder-summary|content-summary|decision-summary|interface-summary|risk-summary|archive-summary-draft>
Summary:
- <3-6 bullets>
Observed:
- <source-backed facts>
Inferred:
- <interpretations, or none>
Unknown:
- <missing source, weak evidence, or none>
Next Use: <persist | review | shape | plan | sync | none>
Recommended Next Task: <persist|review|shape|plan|sync|none>
Persist Candidate: Artifact=distillation; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

Use `Persist Candidate: none` when the summary is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare the summary for persist or next-task use:

```text
User Intent: <one line>
Source Boundaries:
- <source paths, thread, docs, or discussion boundaries>
Summary Focus:
- <focus and audience>
Summary Type:
- <type>
Structured Summary:
- <summary grouped for the requested next use>
Observed:
- <source-backed facts>
Inferred:
- <interpretations with source basis>
Unknown:
- <missing evidence or unresolved ambiguity>
Omitted / Out Of Scope:
- <excluded source or dimensions>
Next Use:
- <persist | review | shape | plan | sync | none>
Recommended Next Task:
- <persist|review|shape|plan|sync|none>
Persist Candidate:
- Artifact=distillation; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs a durable distillation handoff. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load the matching template and shape the final artifact.

```text
Persist Packet:
Artifact: distillation
Thread: <thread or none>
Topic: <topic>
Suggested Target: .session/inbox/distillation_<topic>.md or .session/threads/<thread>/distillation_<topic>.md
Source Summary: <source paths, thread artifacts, docs, discussion, or user-provided material>
Key Fields:
- Summary Focus: <requested focus, audience, and next use>
- Summary Type: <structure-summary | folder-summary | content-summary | decision-summary | interface-summary | risk-summary | archive-summary-draft>
- Key Points: <main summary points>
- Observed / Inferred / Unknown: <short distinction for persist to expand>
- Archive Summary Draft: <only when requested>
Next Use: <persist | review | shape | plan | sync | none>
```

If the distillation is not worth preserving, output `Persist Candidate: none`.

## User Input

{{source, summary focus, summary type, depth, audience, next use, and distill request}}
