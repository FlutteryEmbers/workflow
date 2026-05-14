# Lens: Expand

Use this lens when a short shape, conceptual design, or plan needs to become a detailed draft, or when one large proposal should be split into smaller parts.

## Add Only When Needed

- The proposal is too compressed to review, execute, or hand off.
- A plan needs more detailed steps, examples, pseudocode, validation notes, or handoff detail.
- A design has one large diagram or section that should be split into smaller readable pieces.
- The user asks to expand, elaborate, split, add examples, add pseudocode, or make the draft more detailed.

## Do Not Use For

- Comparing routes; use `strategy`.
- Challenging a proposal; use `redteam`.
- Clarifying language, rules, events, or ownership; use `domain`.
- Adding architecture, domain, or test detail unless the user also selects those lenses.

## Checks

- Preserve the source summary before adding detail.
- Mark source facts separately from assumptions added during expansion.
- Label examples, pseudocode, and draft diagrams clearly.
- Split large diagrams into smaller diagrams when a graph would exceed 7 nodes.
- Keep using same-directory part files even when there are more than 8 parts; maintain the index instead of creating a new directory.
- Keep expanded content draft-status unless the user asks to sync stable conclusions.
- Use the source artifact directory; do not create an expansion-specific directory.

## Output Hints

- Use `workflow_core/templates/expanded.md`.
- Small expansion: `{base}.expanded.md`.
- Split expansion: `{base}.expanded.md` plus `{base}.part_{topic}.md`.
- The main expanded file should include an `Expansion Index`.
- Each part should declare `Source`, `Part Of`, `Part Topic`, `Depends On`, and `Status`.
