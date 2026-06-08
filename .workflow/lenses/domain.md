# Lens: Domain

Use this lens for DDD-lite discussion: language, story flow, events, boundaries, rules ownership, and model iteration.

Do not force tactical DDD patterns such as entities, aggregates, repositories, or value objects unless the user asks.

## Checks

- Language notes: what terms need settled meaning?
- Story flow: what is the user/system flow?
- Events: what changes state or matters to the business?
- Rules ownership: who owns each rule?
- Source of truth: code, project docs, user decision, or unknown?
- Boundary questions: where does responsibility change?
- Model iteration notes: what changed during discussion?

## Output Hints

- Language and model notes can be persisted to `.session/inbox/**`.
- Domain decisions can be persisted to `.session/threads/{thread}/decision_{topic}.md`.
- Project terminology, business rules, and boundaries go to `docs/**` through `sync`.
