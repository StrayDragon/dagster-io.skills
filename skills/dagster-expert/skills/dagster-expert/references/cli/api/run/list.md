---
description: "dg api run list: list runs with filtering and pagination from Dagster Plus."
triggers:
  - "list runs, show runs, all runs"
  - "run history, browse runs, recent runs"
---

```bash
dg api run list
```

- `--status` — filter by run status: QUEUED, STARTING, STARTED, SUCCESS, FAILURE, CANCELING, CANCELED. Repeatable (e.g. `--status FAILURE --status CANCELED`).
- `--job` — filter by job name
