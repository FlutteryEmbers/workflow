# Lens: Redteam / Critique

Use this lens when the user explicitly wants a critique posture: challenge an existing option, shape, plan, decision, claim, or implementation direction by looking for failure paths before it is treated as reliable.

This is not normal review, built-in safety, or a rewrite task. `review` still owns verdicts such as readiness, blocked state, and required evidence. `redteam` only changes the review posture toward deliberate critique.

## Add Only When Needed

- A proposal feels plausible but may hide risk.
- The user points out weaknesses or asks for critique, counterarguments, failure paths, or a hostile read.
- The decision would be costly to reverse.

## Checks

- Stress-test assumptions and evidence.
- Look for failure paths, counterexamples, hidden costs, and long-term maintenance risks.
- Identify boundary breaks, language conflicts, and rule ownership gaps.
- Separate required revisions from acceptable risks.
- Do not issue an implementation-readiness verdict by itself; keep verdict ownership with `review`.
- Do not rewrite the solution unless the user asks for a revised shape or plan.

## Output Hints

- Use the critique structure in chat when useful.
- Persist durable critique results through `Persist Packet` and `persist`, usually as a `review` or `decision` artifact.
- Do not load `.workflow/templates/critique.md` by default in `Mode: discuss`; templates are for `persist` or `sync` persist steps.
