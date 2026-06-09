# sentier-mappings

Cross-source **mappings** for the Sentier platform — the bridges that link
inventory, methods, and background databases at calculation time. A
cross-cutting **Data Layer** repo: it holds loadable artifacts only, no
fetch/parse/calculate code.

- **Fed by:** `sentier-importers` (PR / Push) and the Application Layer.
- **Read by:** `sentier-platform` (resolved link tables at calc time).
- **Format:** [randonneur](https://github.com/brightway-lca/randonneur) JSON
  datapackages — the Brightway data-migration format.

Keeping mappings in their own repo means a new inventory source or method can be
added without touching either side of a bridge, and upstream churn (renamed
flows, renamed processes) is absorbed in one isolated place.

## Layout

```
schema/   # randonneur version pin + conventions + JSON Schemas (the contract)
data/     # randonneur packages, one ranked subfolder per bridge
```

## The three mapping kinds

| Kind | Bridge spans | File | Target encoding |
|---|---|---|---|
| **Foreground → background** | inventory source → ecoinvent | `technosphere.json` | `{database, code}` — opaque code **only** |
| **Process → process** | inventory source → inventory source (Sentier) | `technosphere.json` | full open identifiers |
| **Elementary flow ↔ CF** | inventory flows → method flow keys | `biosphere.json` | full open identifiers |

## No proprietary ecoinvent data

Where a bridge targets ecoinvent, the target carries **only** the Brightway
activity `code` (an opaque, stable pointer) plus a `database` tag — never
`name`, `reference product`, `location`, amounts, or flows. A licensed ecoinvent
holder resolves the code locally at link time. Such bridges set
`"target_proprietary": true` in their `metadata.json`. All non-ecoinvent
identifiers (Sentier processes, method flow keys) are open and stored in full.

## Data

Organized **by bridge**, ranked by a numeric prefix:

```
data/
  01-agribalyse-3.2__ecoinvent-3.9.1/   # foreground→background
  02-agribalyse-3.2__ef-3.1/            # elementary flow↔CF
```

Convention: `data/<NN>-<source>__<target>/`, where `<source>` and `<target>` are
lower-kebab, version-suffixed datasource ids and `<NN>` sets resolution
precedence (lower wins on conflict). Each folder holds one randonneur package per
kind present (`technosphere.json` and/or `biosphere.json`) plus `metadata.json`.
Package payloads are pushed by `sentier-importers`; the skeleton ships `.gitkeep`
+ `metadata.json` stubs only.

## Schema

`schema/` is the contract `sentier-importers` validates against before opening a
delivery PR — a data artifact, not runtime code. It pins the randonneur version,
documents the conventions, and ships JSON Schemas for the package profile and for
`metadata.json`. See [schema/](./schema/).

## License

MIT — open by default, client-loadable.
