# Lens: Iteration

Use this lens when the user wants to co-design through multiple turns before writing final docs.

## Add Only When Needed

- The user is gradually adding background or constraints.
- The recommendation is still changing.
- Options, critiques, and decisions need to stay visible across turns.

## Checks

- Classify new input as background, constraint, option, critique, decision, or open question.
- Keep the current goal and current recommendation visible.
- Track rejected options and why they were rejected.
- Do not converge too early.
- End each major turn with the next move.

## Output Hints

- Use `.workflow/templates/session.md` for the working state.
- Use `.workflow/templates/options.md` when technical routes are being compared.
