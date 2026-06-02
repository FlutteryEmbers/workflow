# Lens: Expand

Use this lens to expand a compact decision, concept, or plan into a more detailed session artifact.

## Add Only When Needed

- A decision is too short to hand off.
- A plan needs examples, pseudocode, smaller diagrams, or split parts.
- A large diagram should be decomposed.

## Expansion Rules

- Preserve the source summary first.
- Mark `Source Facts` separately from `Assumptions Added`.
- Mark examples, pseudocode, and draft diagrams as illustrative unless confirmed.
- Do not automatically apply architecture, domain, or test reasoning unless the user selected those lenses too.
- Prefer diagrams under 7 nodes; split if larger.

## Output Hints

- Expanded artifacts can be persisted next to the source under `.session/threads/{thread}/**`.
- Use `{base}.expanded.md` and optional `{base}.part_{topic}.md`.
- Project docs updates go through `sync`, not direct expansion.
