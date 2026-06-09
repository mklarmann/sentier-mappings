# schema/

The **contract** for the randonneur mapping packages in `../data/`. This is a
data artifact `sentier-importers` reads to validate packages against before
opening a delivery PR — not runtime code. No mapping logic ships here.

| file | describes |
|---|---|
| `randonneur-package.schema.json` | JSON Schema for a mapping package (our randonneur profile) |
| `metadata.schema.json` | JSON Schema for each bridge's `metadata.json` |

## randonneur version

Mapping packages follow the [randonneur](https://github.com/brightway-lca/randonneur)
datapackage format. The exact randonneur spec version this repo targets is
recorded here.

## The three mapping kinds

| kind | verb(s) | file | target encoding |
|---|---|---|---|
| foreground → background | `replace` | `technosphere.json` | `{database, code}` — opaque ecoinvent code only |
| process → process | `replace` | `technosphere.json` | full open identifiers |
| elementary flow ↔ CF | `replace` / `update` | `biosphere.json` | full open identifiers |

- **`replace`** — rewrite a source edge's target to the mapped target.
- **`update`** — adjust fields in place (e.g. unit normalization via
  `conversion_factor`) without changing identity.
- `delete` / `create` are available per the randonneur spec but unused by the
  current skeleton.

## No proprietary ecoinvent data

When a bridge targets ecoinvent (`metadata.json.target_proprietary: true`), every
`target` dict carries **only** `database` + `code` (the Brightway activity code,
an opaque pointer) — never `name`, `reference product`, `location`, amounts, or
flows. A licensed ecoinvent holder resolves the code locally. All other
identifiers (Sentier processes, method flow keys) are open and stored in full.

## Precedence

Bridges resolve in rank order — the `<NN>` prefix on each `data/<NN>-…/` folder
sets precedence (lower wins when two bridges offer conflicting links).
