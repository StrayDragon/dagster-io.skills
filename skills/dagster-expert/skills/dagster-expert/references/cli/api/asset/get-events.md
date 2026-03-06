---
description: "dg api asset get-events: retrieve materialization and observation events for an asset from Dagster Plus."
triggers:
  - "asset events, materialization events, observation events"
  - "asset history, asset partitions, event log"
---

```bash
dg api asset get-events <ASSET_KEY>
```

- `--event-type` — filter by event type (e.g. `ASSET_MATERIALIZATION`, `ASSET_OBSERVATION`)
- `--partition` — filter events by partition key
- `--before` — return events before this timestamp; use with `--limit` to paginate chronologically
