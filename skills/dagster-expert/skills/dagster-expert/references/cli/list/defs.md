---
description: "dg list defs: list and filter registered Dagster definitions."
triggers:
  - "list defs, list assets, show definitions"
  - "filter assets by tag, group, kind"
---

List all registered Dagster definitions (assets, jobs, schedules, sensors, resources) in the current project.

```bash
dg list defs
```

- `--assets <selection>` — filter by asset selection syntax
- `--columns <cols>` — columns to display (comma-separated or repeated flag)
- `--json` — output as JSON instead of a table
- `--response-schema` — print the JSON schema of the response and exit. Use before writing any parsing logic.

**Available columns:** `key`, `group`, `deps`, `kinds`, `description`, `tags`, `cron`, `is_executable`
