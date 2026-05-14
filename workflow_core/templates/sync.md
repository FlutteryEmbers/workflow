# Sync

## Source

{{code, change, raw material, or doc source}}

## Sync Flow

> Optional. Add a Mermaid diagram only when it makes the sync path easier to audit.

```mermaid
flowchart LR
    source_node["Source"] --> sync_node["Sync"]
    sync_node --> current_node[".docs/current"]
    sync_node --> knowledge_node[".docs/knowledge"]
    sync_node --> shared_node[".docs/shared"]
```

## Updates

- {{updated document and reason}}

## Durable Facts

- {{fact promoted to current or shared knowledge}}

## Removed Drift

- {{stale or conflicting information removed}}

## Follow-ups

- {{next action or none}}
