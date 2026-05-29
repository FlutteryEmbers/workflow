# Shape: {{topic}}

## Save Metadata

- Artifact: shape
- Status: {{draft | accepted}}
- Style: {{exploration | audit | summary}}
- Source: {{recent discussion | existing artifact | file path}}
- Target: {{.session/...}}
- Last Updated: {{date}}

## Language / Style

{{default: Chinese explanations with English technical terms preserved; use full English only when requested}}

## Decision Link

{{.session/drafts/shape_{topic}.md, .session/accepted/decision_{topic}.md, or .session/goal/{file}.md}}

## Visual Overview

> Only keep this diagram if it improves readability.

```mermaid
flowchart TD
    input_node["Input"] --> shape_node["Shape"]
    shape_node --> decision_node["Session decision"]
    decision_node --> docs_node["Target docs"]
```

## Problem

{{problem or opportunity}}

## Discussion Trace

- Trigger: {{why this artifact exists}}
- Context Added: {{background that changed the answer}}
- Decision Trail: {{initial direction -> revision -> current direction}}
- Rejected Options: {{compressed list}}
- Open Questions: {{remaining uncertainty}}

## Reframed Goal

{{clearer version of the user's goal}}

## Narrowest Useful Wedge

{{smallest scope that can validate the goal}}

## Success Criteria

- {{what would make this worth continuing}}

## Rejected Larger Scope

- {{larger scope intentionally not included now}}

## Proposed Shape

{{solution shape, concept, architecture, or decision}}

## Boundaries

- In: {{included}}
- Out: {{excluded}}

## Tradeoffs

- {{tradeoff}}

## Target Docs

- {{docs path or none}}

## Open Questions

- {{question}}
