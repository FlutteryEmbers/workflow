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

## Compatibility / Constraint Policy

- Default to `Compatibility: preserve` and `Constraint Mode: respect`.
- `Compatibility: breaking` requires explicit user or accepted-source intent; it means old paths, aliases, migrations, and fallbacks are not preserved unless the plan names them.
- `Constraint Mode: propose_override` means the current constraint may be challenged, but the cost, fallout, and review need must be visible.
- `Constraint Mode: prototype_exception` is temporary PoC scope. It is not a durable architecture rule.
- Do not turn prototype exceptions into project constraints unless explicitly accepted and later synced to `docs/**`.

## Output Hints

- Draft architecture decisions can be persisted to `.session/drafts/**`.
- Accepted architecture decisions can be persisted to `.session/accepted/**` when accepted intent is explicit.
- Goal-level architecture direction may update `.session/goal/vision.md` or `.session/goal/roadmap.md`.
- Accepted project constraints belong in `docs/architecture/boundaries.md` or another user-specified `docs/**` target through `sync`.
- Do not turn prototype exceptions into durable project constraints unless explicitly accepted.
