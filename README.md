# API Commons OpenLint API

A small, **OpenAPI-first HTTP API** from [API Commons](https://apicommons.org) — lint
API artifacts, list the built-in rulesets and supported formats, and validate a
ruleset, all over HTTP. It runs the [OpenLint](https://openlint.org) engine
(`@openlint/*` packages), the same engine as the
[`openlint` CLI](https://github.com/openlint/openlint), so the API, CLI and the
[validator](https://validator.apicommons.org) all agree.

The API **defines itself with OpenAPI** ([`openapi.yaml`](./openapi.yaml)) and serves
it at `GET /openapi.yaml` — so it's lintable by OpenLint, too.

## Run

```bash
npm install
npm start              # http://localhost:8080  (PORT to override)
```

With Docker:

```bash
docker build -t common-openlint-api .
docker run -p 8080:8080 common-openlint-api
```

## Endpoints

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/lint` | Lint an artifact (`{ content, format?, ruleset? }`) → diagnostics + counts |
| `GET` | `/rulesets` | The built-in rulesets (`spectral:oas`, `:asyncapi`, `:arazzo`) and their rules |
| `POST` | `/rulesets/validate` | Structurally validate a ruleset (alias, object, or YAML/JSON string) |
| `GET` | `/formats` | Supported artifact formats |
| `GET` | `/openapi.yaml` | This API's OpenAPI definition |
| `GET` | `/health` | Health check |

### Example

```bash
curl -s -X POST http://localhost:8080/lint \
  -H 'content-type: application/json' \
  -d '{"format":"openapi","content":"openapi: \"3.0.3\"\ninfo: {title: T, version: \"1\"}\npaths: {}\n"}'
```

```json
{ "total": 3, "counts": { "error": 0, "warn": 3, "info": 0, "hint": 0 },
  "diagnostics": [ { "code": "oas3-api-servers", "severity": "warn", "message": "..." } ] }
```

`ruleset` accepts a built-in alias (`"spectral:oas"`, `"spectral:asyncapi"`,
`"spectral:arazzo"`), or a full ruleset definition object (data form) that may
`extends` the built-ins — the [OpenLint ruleset format](https://spec.openlint.org).

**Deprecated aliases.** The old names `spotlight:oas`, `spotlight:asyncapi` and
`spotlight:arazzo` are still accepted (as `ruleset` or inside `extends`) and map to
the `spectral:*` equivalents, so existing callers keep working. `GET /rulesets` lists
only the new names. Switch to `spectral:*`; the old aliases may be removed later.

## Part of the API Commons governance tooling

This API is the HTTP surface other tools can build on. Related:
[common-openlint-validator](https://github.com/api-commons/common-openlint-validator) (browser playground,
[validator.apicommons.org](https://validator.apicommons.org)),
[common-openlint-mcp](https://github.com/api-commons/common-openlint-mcp) (AI),
[common-openlint-pipeline](https://github.com/api-commons/common-openlint-pipeline) (CI), and the
[OpenLint](https://github.com/openlint/openlint) engine and CLI. Rules are catalogued at
[apicommons.org/rules](https://apicommons.org/rules/).

---

A project of [API Evangelist](https://apievangelist.com), maintained openly under [API Commons](https://apicommons.org). Apache-2.0.
