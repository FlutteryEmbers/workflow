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
- Artifact State: {{inbox | working | settled | superseded}}
- Thread: {{thread-name or none}}
- Intent: {{summary | exploration | audit | reference | capture}}
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

- {{fact that affects a later decision, plan, review, or stable-document sync}}

## Execution Trace

{{optional: use only when this note persists build output or execution pitfalls. Include result, changed files, verification trace, pitfalls, likely source, impact, and suggested follow-up. This is factual trace, not a review verdict, and must not be used as an execution plan.}}

## Execution Discovery

{{optional: use only for `Artifact State: inbox` and `Intent: capture` when preserving reusable build execution knowledge.}}

- Source Build: {{plan path, inline build request, command, or discussion}}
- OS / Shell: {{OS and shell, or unknown}}
- CWD: {{working directory used or discovered}}
- Command: {{verification/build command or none}}
- Command Source: {{plan | package script | Makefile | project docs | CI | repo fact | none}}
- Retry Budget: {{used/limit or none}}
- Result: {{completed | partial | blocked | failed | not run}}
- Reusable Lesson: {{non-obvious fact that reduces future trial-and-error}}
- Future Use: {{how future build, review, plan, sync, or agent work should use this}}
- Promotion Candidate: {{thread note | project docs | code README | build adapter | none}}

## Assumptions

- {{assumption or none}}

## Open Questions

- {{question}}

## Possible Decisions

- {{decision candidate or none}}

## Target Docs

- {{docs path or none}}

> Inbox notes are working memory and untriaged knowledge capture under `.session/inbox/**`. Explicit `notes/**` targets are disposable exploration notes. Neither is project source of truth or an execution plan.
