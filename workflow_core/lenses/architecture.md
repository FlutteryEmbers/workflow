# Lens: Architecture

Use this lens when structure, dependency direction, interfaces, or long-term constraints matter.

This lens is a review method, not a rule repository. Use it to reason about architecture during `explore`, `shape`, `plan`, or `review`.

## Add Only When Needed

- Multiple modules or boundaries are affected.
- A public interface or data contract may change.
- A decision would be expensive to reverse.

## Checks

- Identify affected boundaries.
- Compare at least one simpler alternative.
- Record the chosen tradeoff.

## Durable Constraints

- Do not write long-term constraints from this lens directly.
- Use `sync --lens architecture` to record confirmed constraints in `.docs/shared/boundaries.md`.
- Keep speculative architecture notes in `.docs/work/shapes/` or `.docs/work/decisions/`.
