---
name: Search the data catalog
description: Query Artie's data catalog to discover databases, schemas, tables, and columns across connected sources.
api: openapi/artie-openapi-original.yml
operations:
  - POST /data-catalog/search
---

# Search the data catalog

Base URL: `https://api.artie.com`
Auth: `Authorization: Bearer $ARTIE_API_KEY`.

## Steps

1. **Search** — `POST /data-catalog/search` with a `PayloadsDataCatalogSearchRequest` body (search term / filters).
2. **Read the matches** — the response is a `ListResponseBody` of `PayloadsDataCatalogMatch` items, each resolving to a matched database / schema / table / column object (`PayloadsDataCatalogMatchedObject`).

## Rules

- Auth is a bearer API key on every call.
- The response envelope is `{ "items": [...] }` (no cursor/offset paging is documented — the full result set is returned). See `conventions/artie-conventions.yml`.
- Errors return `{ "error": "<message>" }` — see `errors/artie-problem-types.yml`.
