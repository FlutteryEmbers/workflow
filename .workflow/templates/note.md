# Note

> Optional for explicit `Target: notes/**` disposable exploration notes:
>
> ```yaml
> status: active | parked | discarded | promoted
> source: chat | script | code | external
> updated: {{date}}
> promoted_to: {{path or none}}
> ```

## Persist Metadata

- Artifact: note
- Topic: {{topic}}
- Status: {{inbox | working | stable | superseded}}
- Thread: {{thread-name or none}}
- Intent: {{summary | exploration | audit | reference}}
- Depth: {{compact | standard}}
- Source: {{recent discussion | user input | file path}}
- Target: {{.session/...}}
- Last Updated: {{date}}

## Purpose

{{why this note exists}}

## Why This Matters

{{why this note should be persisted instead of remaining only in chat}}

## Source

- {{user input, code path, reference doc, project docs, or discussion}}

## Observations

- {{observed fact or staged requirement}}

## Useful Facts

- {{fact that affects a later decision, plan, review, or docs sync}}

## Execution Summary

{{optional: use only when this note persists build output or execution pitfalls. Include result, changed files, verification, pitfalls, likely source, impact, and suggested follow-up. This is a factual summary, not a review verdict, and must not be used as an execution plan.}}

## Assumptions

- {{assumption or none}}

## Open Questions

- {{question}}

## Possible Decisions

- {{decision candidate or none}}

## Target Docs

- {{docs path or none}}

> Inbox notes are working memory under `.session/inbox/**`. Explicit `notes/**` targets are disposable exploration notes. Neither is project source of truth.
