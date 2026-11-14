# IAOP Axum Service Template

Canonical scaffold: `iaop-rust-common/templates/axum-microservice/`
(generator `scaffold.py`). Every business/platform service repo is
produced from it with service-specific naming substituted.

## Required endpoints

| Path | Purpose |
|---|---|
| `GET /health/live` | liveness — process up, always 200 `{"status":"ok"}` |
| `GET /health/ready` | readiness — DB reachable; 503 + problem details when not |
| `GET /api/v1/system/info` | service name, version, environment |
| `GET /openapi.json` | utoipa-generated OpenAPI 3.1 |
| `GET /docs` | Scalar UI |

## Required middleware (outermost first)

1. `TraceLayer` (tower-http) with request IDs
2. correlation/request-id middleware (`X-Correlation-Id`, `X-Request-Id`,
   `traceparent` propagation)
3. `CatchPanicLayer` + `TimeoutLayer` (configurable)
4. body limit, JSON content-type enforcement
5. auth extractor boundary (JWT validation via `iaop-security`) — routes
   declare auth requirements; health endpoints are public

## Error model

Single `ApiError` -> RFC 9457 Problem Details mapping:
`400 validation`, `401 unauthenticated`, `403 forbidden`, `404 not_found`,
`409 conflict`, `422 unprocessable`, `429 rate_limited`, `500 internal`,
`503 unavailable`. Codes are stable SCREAMING_SNAKE strings.

## Crate layout (per service)

```
crates/<svc>-domain/         # entities, value objects, domain errors, policies
crates/<svc>-application/    # use cases, ports (traits), DTOs
crates/<svc>-infrastructure/ # sqlx repos, outbox store, HTTP clients, broker adapters
crates/<svc>-api/            # axum router, handlers, extractors, OpenAPI doc
src/main.rs                  # composition root: config -> infra -> app -> serve
```

`main.rs` responsibilities: load+validate config, init tracing/OTel,
build pools/clients, run pending migrations (or defer to job), mount
router, wire graceful shutdown (SIGTERM/SIGINT + in-flight drain timeout).

## Persistence boundary

Repository traits are defined in `application` (ports); implementations
in `infrastructure` use SQLx against the service's own database only.
Outbox/inbox tables are provisioned by the service's own migrations.
