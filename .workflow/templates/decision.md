# Decision

## Persist Metadata

- Artifact: decision
- Topic: {{topic}}
- Artifact State: {{working | settled | superseded}}
- Thread: {{thread-name}}
- Intent: {{decision | constraint | handoff | audit}}
- Depth: {{detailed}}
- Source: {{recent discussion | existing artifact | file path}}
- Target: {{.session/...}}
- Last Updated: {{date}}

## Language / Style

{{default: Chinese explanations with English technical terms preserved; use full English only when requested}}

## Type

{{direction | scope | plan | constraint | handoff | review}}

## Artifact State

{{working | settled | superseded}}

## Acceptance

- Confirmed By: {{user | review | explicit instruction | none}}
- Source Artifact: {{thread artifact or discussion}}
- Effective Scope: {{where this decision applies}}

## Decision Context

{{why this decision exists and what inputs it uses}}

## Source Context

- {{discussion, thread artifact, review, code path, project doc, or user decision source}}

## Discussion Notes To Preserve

{{preserve discussion details that would help future readers understand, revise, implement, or audit this decision. Include user corrections, preferences, examples, important constraints, accepted risks, or why the decision changed when they matter. Do not preserve full transcript or conversational noise.}}

## Decision-Relevant Facts

- {{confirmed fact that materially affects this decision}}

## Assumptions vs Facts

- Fact: {{confirmed input}}
- Assumption: {{inference that still needs validation}}

## Success Criteria

- {{what must be true for this decision to be considered successful}}

## Decision

{{the chosen direction, scope, plan, constraint, handoff, or review decision}}

## Discussion Trace

- Trigger: {{why this decision exists}}
- Context Added: {{background that changed the decision}}
- Decision Trail: {{initial direction -> revision -> current decision}}
- Rejected Options: {{compressed list}}
- Open Questions: {{remaining uncertainty}}

## Decision Trail

{{how the decision emerged from discussion, evidence, review, or user instruction}}

## Rationale

{{why this is the current recommendation}}

## Evidence

- {{evidence, source, code path, doc, review finding, or user confirmation}}

## Constraints

- {{constraint this decision must respect}}

## Alternatives Considered

| Alternative | Why Not | State |
| :--- | :--- | :--- |
| {{alternative}} | {{reason}} | {{rejected / parked / superseded}} |

## Implications

- {{impact, tradeoff, or required follow-up}}

## Consequences

- {{expected operational, architectural, product, or docs consequence}}

## Validity / Review Trigger

- {{condition that should cause this decision to be revisited}}

## How To Use This Decision

{{how future shape, plan, build, review, sync, or external-agent work should use this decision}}

## Target Docs

- {{docs path or none}}

## Open Questions

- {{question}}

## Next Use

{{plan, build, external-agent, review, sync, or none}}

## Source

- {{session note, goal file, project doc, code path, or user input}}
