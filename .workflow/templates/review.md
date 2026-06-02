# Review

## Persist Metadata

- Artifact: review
- Topic: {{topic}}
- Status: {{working | stable | superseded}}
- Thread: {{thread-name}}
- Intent: {{audit | decision}}
- Depth: {{detailed}}
- Source: {{recent discussion | existing artifact | diff | file path}}
- Target: {{.session/...}}
- Last Updated: {{date}}

## Language / Style

{{default: Chinese explanations with English technical terms preserved; use full English only when requested}}

## Review Target

{{code, docs, session decision, external plan, diff, or behavior claim}}

## Review Question

{{what this review is trying to decide}}

## Source Context

- {{plan, diff, code path, project doc, session artifact, or user claim}}

## Evidence Checked

- {{file, diff, command output, doc, artifact, or discussion evidence}}

## Decision-Relevant Facts

- {{fact that materially changes the verdict}}

## Assumptions vs Facts

- Fact: {{confirmed input}}
- Assumption: {{inference that still needs validation}}

## Discussion Trace

- Trigger: {{why this review exists}}
- Context Added: {{background that changed the verdict}}
- Decision Trail: {{initial concern -> evidence -> verdict}}
- Rejected Options: {{fixes or interpretations rejected}}
- Open Questions: {{remaining uncertainty}}

## Reasoning Trail

{{how evidence changed or confirmed the verdict}}

## Decision

{{ready | needs changes | blocked | docs blocked | stable | revise}}

## Readiness

- Confidence: {{high | medium | low}}
- Readiness: {{0-10}}
- Blocking Gaps: {{must-fix before next write or implementation}}
- Non-blocking Gaps: {{can track without blocking}}
- Recommended Action: {{none | persist | sync | shape | plan | build | external-agent}}
- Can Promote Source: {{yes/no}}
- Can Execute Plan: {{yes/no}}

## Findings

| Severity | Finding | Evidence | Recommended Action |
| :--- | :--- | :--- | :--- |
| {{severity}} | {{finding}} | {{evidence}} | {{action}} |

## What Is Still Reasonable

- {{part of the target that can remain unchanged}}

## Required Revisions

- {{required change before readiness or next write}}

## Open Questions

- {{question that prevents readiness, execution, or sync}}

## Failure Or Risk Path

> Optional. Keep this diagram only if it makes the finding easier to understand.

```mermaid
flowchart TD
    trigger_node["Trigger"] --> risk_node["Risk"]
    risk_node --> impact_node["Impact"]
```

## Project Docs Rules Check

{{only when docs/** is involved}}

- Source clear: {{yes/no}}
- Future use clear: {{yes/no}}
- Source of truth clear: {{yes/no}}
- Future-use success criteria clear: {{yes/no}}
- Existing docs tone and structure preserved: {{yes/no}}
- Session-only material excluded: {{yes/no}}

## Docs Follow-up

- Impact: {{none | suggested | required}}
- Target: {{docs/** path or none}}
- Reason: {{why future human/agent execution could be misled without docs update}}
- Recommended Sync: {{sync prompt or none}}

## Verification

- {{verification performed or needed}}

## Follow-up

- {{persist, sync, shape, plan, build, external-agent, or none}}

## Recommended Next Task

{{shape | plan | build | sync | persist | external-agent | none}}

## Next Use

{{persist, plan, build, sync, or none}}
