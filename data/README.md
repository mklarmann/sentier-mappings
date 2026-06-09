# data/

[randonneur](https://github.com/brightway-lca/randonneur) JSON mapping packages,
**one subfolder per bridge**, ranked by a numeric prefix:

```
data/
  01-agribalyse-3.2__ecoinvent-3.9.1/   # rank 1 — highest resolution precedence
  02-agribalyse-3.2__ef-3.1/
```

Convention: `data/<NN>-<source>__<target>/`, where `<source>` and `<target>` are
lower-kebab, version-suffixed datasource ids (`agribalyse-3.2`,
`ecoinvent-3.9.1`, `ef-3.1`) and `<NN>` sets resolution precedence (lower wins
when two bridges offer conflicting links). The rank prefix is **not** part of any
datasource id.

A **bridge** maps one source identifier space onto one target space. Each folder
holds one randonneur package file **per kind present**:

| file | kind | declared in `metadata.json.kinds` as |
|---|---|---|
| `technosphere.json` | foreground→background or process→process | `technosphere` |
| `biosphere.json` | elementary flow ↔ CF | `biosphere` |
| `metadata.json` | bridge identity + flags | — |

See [../schema/](../schema/) for the package profile and the `metadata.json`
schema. Package payloads (`technosphere.json` / `biosphere.json`) are pushed by
`sentier-importers`; the skeleton ships `.gitkeep` + `metadata.json` stubs only.
