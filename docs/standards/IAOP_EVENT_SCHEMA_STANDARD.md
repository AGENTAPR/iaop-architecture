# IAOP Event Schema Standard

## Envelope

Canonical schema: `iaop-event-contracts/envelope.schema.json`.
Rust mirror: `iaop-messaging::EventEnvelope` (camelCase wire format).
Test builder: `iaop-test-kit::events::TestEventEnvelope`.

Fields: `eventId` (uuid), `eventType` (`<context>.<aggregate>.<verb>`),
`eventVersion` (int >=1), `source` (service name), `aggregateId?`,
`aggregateVersion?`, `occurredAt` (RFC3339 UTC), `correlationId?`,
`causationId?`, `payload` (object, schema per event type).

## Event documents

`iaop-event-contracts/events/<context>/<event-type>.schema.json` —
JSON Schema 2020-12, `$id` matching the event type, `payload` schema
embedded via `$ref` or inline. Catalog: `catalog.json` / `catalog.md`.

## Rules

- Immutable once published: changes to payload shape = new
  `eventVersion`; breaking changes never mutate an existing version.
- Events are facts (past tense): `agent.activated`,
  `compliance.restriction-applied`.
- Produced via transactional outbox in the same DB transaction as the
  state change; consumed via inbox with dedup on `eventId`.
- `correlationId` propagates request trace; `causationId` links
  cause->effect chains across services.
- PII minimization: payloads carry IDs/references, not documents.

## Validation

`iaop-event-contracts/tools/validate.py` validates every schema compiles
as 2020-12 and envelope compatibility (required fields, formats).
