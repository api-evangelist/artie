---
name: Configure column encryption and hashing
description: Protect sensitive columns in an Artie pipeline with encryption keys and deterministic column hashing salts.
api: openapi/artie-openapi-original.yml
operations:
  - GET /encryption-keys
  - POST /encryption-keys
  - GET /column-hashing-salts
  - POST /column-hashing-salts
  - POST /column-hashing-salts/{uuid}/preview
  - POST /column-hashing-salts/{uuid}
---

# Configure column encryption and hashing

Base URL: `https://api.artie.com`
Auth: `Authorization: Bearer $ARTIE_API_KEY`.

## Steps

1. **List existing encryption keys** — `GET /encryption-keys` to avoid duplicates (KMS-managed keys are supported).
2. **Create an encryption key** — `POST /encryption-keys`; capture the `uuid`.
3. **List existing hashing salts** — `GET /column-hashing-salts`.
4. **Create a column hashing salt** — `POST /column-hashing-salts` for deterministic one-way hashing of a sensitive column.
5. **Preview the transform** — `POST /column-hashing-salts/{uuid}/preview` to confirm the hashed output before applying it to a live pipeline.
6. **Update if needed** — `POST /column-hashing-salts/{uuid}` (note: update is a POST to the resource path, not PUT/PATCH).

## Rules

- Resources are keyed by `uuid`. Update semantics use `POST /{resource}/{uuid}`.
- Always run the `/preview` step before persisting a hashing change to a running pipeline.
- Errors return `{ "error": "<message>" }` — see `errors/artie-problem-types.yml`.
