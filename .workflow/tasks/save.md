---
id: save
role: steward
purpose: Save distilled session artifacts from discussion, drafts, or user-provided sources.
inputs:
  - artifact
  - status
  - style
  - topic
  - source
  - target
  - request
outputs:
  - .session/inbox/{artifact}_{topic}.md
  - .session/drafts/{artifact}_{topic}.md
  - .session/accepted/{artifact}_{topic}.md
user_selectable_lenses:
  - iteration
  - language
done_check:
  - artifact_kind_is_named
  - status_is_clear
  - target_boundary_is_valid
---

# Save Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: persist` is required for writes.
- `Mode: discuss` may propose a save target, artifact kind, status, and template without writing.
- `Mode: execute` is not valid for this task.
- Load templates only for the artifact being saved.

## When To Use

- Use after `clarify`, `explore`, `shape`, `plan`, or `review` discussion when the user wants to save a session artifact.
- Use to update an existing `.session/**` artifact with newer discussion, review feedback, or user corrections.
- Use to promote a draft to `.session/accepted/**` only when the user explicitly says accepted, approved, promote, or equivalent.

## Do Not Use When

- Do not write formal docs; use `sync` for `docs/**` and `src/**/README.md`.
- Do not modify source code, `.workflow/**`, `.github/**`, or other repository artifacts; use `build` or external-agent.
- Do not save full transcripts by default.

## Save Inputs

- `Artifact`: `brief | note | shape | option | plan | review | decision | distillation | expanded | goal`.
- `Status`: `inbox | draft | accepted`.
- `Style`: `summary | exploration | audit | constraint | handoff`.
- `Topic`: short file-safe topic.
- `Source`: recent discussion, existing artifact, user input, file path, or selected context.
- `Target`: optional for `.session/inbox/**` and `.session/drafts/**`; explicit for `.session/accepted/**` unless accepted intent is already clear.

## Target Rules

- Infer `.session/inbox/{artifact}_{topic}.md` for `Status: inbox`.
- Infer `.session/drafts/{artifact}_{topic}.md` for `Status: draft`.
- Infer `.session/accepted/{artifact}_{topic}.md` only when accepted intent is explicit.
- Write `.session/goal/**` only when the user explicitly provides that target.
- Respect explicit `.session/**` targets even when the file name does not follow the recommended prefix; include a naming note instead of blocking.

## Explicit Target Override

If the user explicitly provides a target path, respect it unless it violates write safety, lacks required prerequisites, or belongs to another write boundary.

- `.session/**`: `save` may write it when the request is a session artifact.
- `docs/**` or `src/**/README.md`: route to `sync` and require Formal Docs Rules.
- Source code, `.workflow/**`, `.github/**`, or other repository artifacts: route to `build` or external-agent with an approved plan.

## Conversation Handling

- Preserve decision trail, not transcript.
- Include discussion content only when it explains why the artifact exists, why the recommendation changed, why options were rejected, or what remains unresolved.
- If the user asks to save the process, save a conversation summary by default.
- Save verbatim transcript only when the user explicitly asks for verbatim transcript, and note that it is noisy and not source of truth.

## Template Selection

- `brief`: `.workflow/templates/brief.md`
- `note`: `.workflow/templates/note.md`
- `shape`: `.workflow/templates/shape.md`
- `option`: `.workflow/templates/options.md`
- `plan`: `.workflow/templates/plan.md`
- `review`: `.workflow/templates/review.md`
- `decision`: `.workflow/templates/decision.md`
- `distillation`: `.workflow/templates/distillation.md`
- `expanded`: `.workflow/templates/expanded.md`
- `goal`: `.workflow/templates/goal.md`

## Output Rules

- Write only the target session artifact.
- Add `Save Metadata` to the artifact.
- Keep `Style` as metadata; do not use it to choose directories or permissions.
- Draft artifacts are not approved execution sources.
- Accepted artifacts are session-level accepted sources, but formal long-term docs still go through `sync`.

## User Input

{{artifact, status, style, topic, source, target, and save request}}
