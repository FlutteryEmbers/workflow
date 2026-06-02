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
- Use `persist` for staged context in `.session/inbox/**`.
- Use `persist` for work-in-progress and stable judgments in `.session/threads/{thread}/**`.
- Do not sync every iteration; sync project docs only after a decision is stable enough for `docs/**`.
