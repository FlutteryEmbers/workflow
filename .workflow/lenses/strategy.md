# Lens: Strategy

Use this lens when the user wants AI to propose and compare technical routes or design directions.

## Add Only When Needed

- Multiple viable technical approaches exist.
- The user asks for a recommendation.
- Tradeoffs, costs, risks, or reversibility matter.

## Checks

- Compare 2-4 options.
- State when each option fits and does not fit.
- Record cost, risk, evidence, and unknowns.
- Explain the current recommendation.
- Name what would change the recommendation.
- Keep the recommendation reversible until the user converges.

## Output Hints

- Use `.workflow/templates/options.md` for option comparison.
- Use `.workflow/templates/shape.md` only when the direction is settled enough to shape.
