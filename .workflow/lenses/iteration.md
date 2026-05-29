# Lens: Iteration

Use this lens when work evolves through multi-turn conversation and changing background.

## Add Only When Needed

- The user is progressively adding context, constraints, options, critiques, or decisions.
- The current recommendation may change.
- Session state needs to stay visible across turns.

## Classify New Input

- Background
- Constraint
- Option
- Critique
- Decision
- Open question
- Target docs impact

## Output Hints

- Keep current goal visible from `.session/goal/**`.
- Use `save` for staged context in `.session/inbox/**`.
- Use `save` for work-in-progress judgments in `.session/drafts/**`.
- Use `save` for accepted session judgments in `.session/accepted/**` when accepted intent is explicit.
- Do not sync every iteration; sync formal docs only after a decision is stable enough for `docs/**`.
