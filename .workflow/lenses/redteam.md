# Lens: Redteam

Use this lens when the user wants to challenge a current option, model, plan, or recommendation.

## Add Only When Needed

- A proposal feels plausible but may hide risk.
- The user points out weaknesses or asks for critique.
- The decision would be costly to reverse.

## Checks

- Stress-test assumptions and evidence.
- Look for failure modes, hidden costs, and long-term maintenance risks.
- Identify language conflicts, boundary conflicts, and rule ownership gaps.
- Separate required revisions from acceptable risks.
- Do not rewrite the solution unless the user asks for a revised shape or plan.

## Output Hints

- Use the critique structure in chat when useful.
- Persist durable critique results through `Save Packet` and `save`, usually as a `review` or `decision` artifact.
- Do not load `.workflow/templates/critique.md` by default in `Mode: discuss`; templates are for `save` or `sync` persist steps.
