# Lens: Test

Use this lens when behavior needs stronger verification before or during implementation.

## Add Only When Needed

- A behavior change can regress existing flows.
- Acceptance criteria are unclear or risky.
- The user asks for test-first planning.
- Refactor or migration work needs higher assurance than default minimum viable verification.

## Checks

- State observable behavior.
- Name automated or manual checks.
- Record what was verified and what was not.
- Raise verification from minimum viable checks to higher assurance when justified.
- Consider contract baseline, old/new parity, regression matrix, golden cases, e2e, edge cases, rollback validation, and migration validation.
- Name when stronger assurance is unavailable and what residual risk remains.

## Boundary

This lens strengthens verification only. It does not grant write, build, sync, or review authority, and it does not make default `plan` require high-assurance verification when the lens is not selected.
