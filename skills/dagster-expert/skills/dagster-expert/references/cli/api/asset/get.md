---
description: "dg api asset get: retrieve details for a specific asset from Dagster Plus."
triggers:
  - "asset details, get asset, asset info"
  - "asset status, asset metadata"
---

```bash
dg api asset get <ASSET_KEY>
```

For prefixed asset keys, use slash-separated syntax: `dg api asset get my_prefix/my_asset`.

`--status` — includes materialization status information. Not included by default as it requires additional API calls.
