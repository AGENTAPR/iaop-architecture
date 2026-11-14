# IAOP Database Migration Standard

## Rules

- PostgreSQL per service; SQLx migrations in `migrations/`.
- Filenames `NNNN_short_description.sql`, zero-padded, monotonically
  increasing, **forward-only** — never edit an applied migration; add a
  new one to fix.
- Migrations run inside a transaction where possible; avoid
  non-transactional statements (e.g. `CREATE INDEX CONCURRENTLY`) unless
  documented.
- Every service provisions its own `outbox`/`inbox` tables via its own
  migrations — no shared migration code, no cross-service FKs.
- `sqlx::migrate!()` embeds migrations at compile time.

## Conventions

- Snake_case tables/columns; `id uuid primary key default gen_random_uuid()`
  or service-chosen ULID/UUIDv7.
- `created_at timestamptz not null default now()`,
  `updated_at timestamptz` maintained by trigger or application.
- Soft-delete only where the domain requires it; document the choice.
- Indexes named `<table>_<cols>_idx`; constraints named
  `<table>_<purpose>`.

## Verification

- `cargo test` includes a migration smoke test applying all migrations
  to a scratch database when `DATABASE_URL` is set; otherwise a compile
  check of `sqlx::migrate!()`.
- `sqlx migrate info` equivalent run in CI against ephemeral Postgres
  (documented in `docs/database.md` per service).
