# Lens: Architecture

Use this lens when structure, boundaries, dependencies, interfaces, or durable technical constraints matter.

## Add Only When Needed

- A decision affects module boundaries, dependency direction, public surfaces, data ownership, or deployment shape.
- A plan may cross architectural boundaries.
- A review should check structural risk or constraint drift.

## Checks

- Boundary: what owns the responsibility?
- Direction: which dependencies are allowed or forbidden?
- Surface: what public API, contract, CLI, UI, or doc surface changes?
- Data: who owns reads, writes, lifecycle, and migration?
- Constraint: which durable project constraints must be preserved?
- Exception: is this a prototype exception or a durable constraint?

## Output Hints

- Draft architecture decisions can be persisted to `.session/drafts/**`.
- Accepted architecture decisions can be persisted to `.session/accepted/**` when accepted intent is explicit.
- Goal-level architecture direction may update `.session/goal/vision.md` or `.session/goal/roadmap.md`.
- Accepted project constraints belong in `docs/architecture/boundaries.md` or another user-specified `docs/**` target through `sync`.
- Do not turn prototype exceptions into durable project constraints unless explicitly accepted.
