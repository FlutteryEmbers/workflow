# Options

## Save Metadata

- Artifact: option
- Status: {{draft | accepted}}
- Style: {{exploration | audit}}
- Source: {{recent discussion | existing artifact | file path}}
- Target: {{.session/...}}
- Last Updated: {{date}}

## Decision Question

{{what choice is being compared}}

## Discussion Trace

- Trigger: {{why these options were compared}}
- Context Added: {{background that changed the comparison}}
- Decision Trail: {{initial option set -> revision -> current recommendation}}
- Rejected Options: {{compressed list}}
- Open Questions: {{remaining uncertainty}}

## Option Map

> Optional. Add this only when option relationships are easier to scan visually.

```mermaid
flowchart TD
    decision_node["Decision Question"] --> option_a["Option A"]
    decision_node --> option_b["Option B"]
    decision_node --> option_c["Option C"]
    option_a --> recommendation_node["Current Recommendation"]
```

## Options

| Option | Fit | Cost | Risk | Evidence | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| {{option}} | {{where it fits}} | {{cost}} | {{risk}} | {{evidence}} | {{current | rejected | parked}} |

## Model Fit

| Option | Fits Concept Model | Boundary Impact | Tradeoff |
| :--- | :--- | :--- | :--- |
| {{option}} | {{yes/no/partial}} | {{impact}} | {{tradeoff}} |

## Current Recommendation

{{recommended option and why}}

## Why Not The Others

- {{option}}: {{reason}}

## What Would Change The Decision

- {{new fact, constraint, or risk that would change the recommendation}}
