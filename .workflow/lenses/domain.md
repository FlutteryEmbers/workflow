# Lens: Domain

Use this lens for DDD-lite discussion: language, story flow, events, boundaries, rules ownership, and model iteration.

Do not force tactical DDD patterns such as entities, aggregates, repositories, or value objects unless the user asks.

## Checks

- Language notes: what terms need stable meaning?
- Story flow: what is the user/system flow?
- Events: what changes state or matters to the business?
- Rules ownership: who owns each rule?
- Source of truth: code, formal docs, user decision, or unknown?
- Boundary questions: where does responsibility change?
- Model iteration notes: what changed during discussion?

## Output Hints

- Draft language and model notes can be saved to `.session/inbox/**`.
- Draft domain decisions can be saved to `.session/drafts/**`.
- Accepted domain decisions can be saved to `.session/accepted/**` when accepted intent is explicit.
- Formal terminology, business rules, and boundaries go to `docs/**` through `sync`.
