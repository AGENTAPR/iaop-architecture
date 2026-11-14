# IAOP API Contract Standard

## Documents

`iaop-api-contracts/services/<service>/openapi.yaml` for business
services and `iaop-api-contracts/services/platform/<service>/openapi.yaml`
for platform services/BFFs — one OpenAPI 3.1 document per service.
Service code generates its spec via utoipa; the contract repo is the
reviewed source of truth for consumers.

## Rules

- Base path `/api/v1`; breaking changes require `/api/v2` (never mutate v1
  semantics).
- Every operation has a unique `operationId` within its document
  (camelCase, e.g. `getSystemInfo`).
- Every operation documents `summary`, `tags`, and response schemas.
- Errors: `application/problem+json` bodies with `type`, `title`, `status`,
  `detail`, `instance`, `code`.
- Auth: `bearerAuth` (JWT) security scheme; public endpoints declare
  `security: []` explicitly.
- Pagination: cursor-based `page[after]`/`page[limit]` or documented
  offset only where appropriate; list responses include `data` + `meta`.
- Idempotency: mutating endpoints accept `Idempotency-Key` where retries
  are expected (documented per-operation).
- Versioning of events is separate — see IAOP_EVENT_SCHEMA_STANDARD.md.

## Validation

`iaop-api-contracts/tools/validate.py` checks: YAML parses, OpenAPI 3.1
structure, unique per-document operationIds, all operations have
operationId/summary/tags, error responses reference problem schema.
