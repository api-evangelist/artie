---
name: Create and start a replication pipeline
description: Stand up an Artie change-data-capture pipeline from a source database to a destination warehouse and start replication.
api: openapi/artie-openapi-original.yml
operations:
  - POST /source-readers
  - POST /connectors
  - POST /pipelines/validate-unsaved-source
  - POST /pipelines/validate-unsaved-destination
  - POST /pipelines/create-from-source
  - POST /pipelines
  - POST /pipelines/{uuid}/start
  - GET /pipelines/{uuid}
---

# Create and start a replication pipeline

Base URL: `https://api.artie.com`
Auth: send `Authorization: Bearer $ARTIE_API_KEY` on every request (scheme `ApiKey`, HTTP bearer).

## Steps

1. **Validate the source** — `POST /pipelines/validate-unsaved-source` with the source connection details before persisting anything.
2. **Validate the destination** — `POST /pipelines/validate-unsaved-destination`.
3. **Create the source connector** — `POST /connectors` (source database: Postgres/MySQL/MongoDB/Oracle/DynamoDB). Capture the returned `uuid`.
4. **Create the source reader** — `POST /source-readers` bound to the connector; deploy it with `POST /source-readers/{uuid}/deploy` if a deploy step is required.
5. **Create the pipeline** — `POST /pipelines` (or `POST /pipelines/create-from-source` to derive one from a validated source), wiring source reader + destination connector + table config. Capture the pipeline `uuid`.
6. **Start it** — `POST /pipelines/{uuid}/start`.
7. **Confirm status** — `GET /pipelines/{uuid}` and check `status` (`draft` → `running`; see enum `draft|paused|transfer paused|running`).

## Rules

- All resources are addressed by `uuid` path parameters.
- Errors return `{ "error": "<message>" }` (not RFC 9457) — surface the `error` string. See `errors/artie-problem-types.yml`.
- No idempotency key is supported; do not blindly retry a create on timeout — re-list first (`GET /pipelines`) to avoid duplicates. See `conventions/artie-conventions.yml`.
- Subscribe to webhooks (`backfill.*`, `replication.*`) for async progress rather than polling. See `asyncapi/artie-webhooks-asyncapi.yml`.
