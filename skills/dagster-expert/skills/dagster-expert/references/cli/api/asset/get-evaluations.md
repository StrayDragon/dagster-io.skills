---
description: "dg api asset get-evaluations: retrieve automation condition evaluation records for an asset from Dagster Plus."
triggers:
  - "declarative automation, automation condition history"
  - "declarative automation, automation condition debugging"
---

```bash
dg api asset get-evaluations <ASSET_KEY>
```

`--include-nodes` — includes individual evaluation nodes in the response. Warning: this produces dense output with the full tree of conditions evaluated for each record. Only use when processing a small number of records.
