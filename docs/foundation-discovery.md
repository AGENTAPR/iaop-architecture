# IAOP Foundation Discovery Report

Date: 2026-11-13 · Auditor: Devin · Toolchain: rustc/cargo 1.100.0 (stable-x86_64-pc-windows-msvc), Docker 29.9.2, gh 2.103.3

## Environment

- GitHub CLI authenticated as `irsyadjpp`, org `AGENTAPR` membership: active admin.
- All 28 target repositories exist, are **private**, default branch `main`.
- Local clones created under `iaop-workspace/` (shallow).

## Repository-by-repository assessment

Every repository contains only bootstrap documentation (`README.md`,
`.gitignore`, `.editorconfig`, `.github/pull_request_template.md`, and
group-specific placeholder READMEs). **No Cargo manifests, no source code, no
migrations, no workflows, no contracts** exist in any repository.

| # | Repository | Contents found | Status |
|---|---|---|---|
| 01 | iaop-recruitment-service | README only | NEEDS_INITIALIZATION |
| 02 | iaop-onboarding-service | README only | NEEDS_INITIALIZATION |
| 03 | iaop-agent-service | README only | NEEDS_INITIALIZATION |
| 04 | iaop-agency-service | README only | NEEDS_INITIALIZATION |
| 05 | iaop-learning-service | README only | NEEDS_INITIALIZATION |
| 06 | iaop-certification-service | README only | NEEDS_INITIALIZATION |
| 07 | iaop-eligibility-service | README only | NEEDS_INITIALIZATION |
| 08 | iaop-sales-service | README only | NEEDS_INITIALIZATION |
| 09 | iaop-portfolio-service | README only | NEEDS_INITIALIZATION |
| 10 | iaop-commission-service | README only | NEEDS_INITIALIZATION |
| 11 | iaop-compliance-service | README only | NEEDS_INITIALIZATION |
| 12 | iaop-performance-service | README only | NEEDS_INITIALIZATION |
| 13 | iaop-api-gateway | README only | NEEDS_INITIALIZATION |
| 14 | iaop-agent-bff | README only | NEEDS_INITIALIZATION |
| 15 | iaop-operations-bff | README only | NEEDS_INITIALIZATION |
| 16 | iaop-workflow-service | README only | NEEDS_INITIALIZATION |
| 17 | iaop-integration-service | README only | NEEDS_INITIALIZATION |
| 18 | iaop-notification-service | README only | NEEDS_INITIALIZATION |
| 19 | iaop-document-service | README only | NEEDS_INITIALIZATION |
| 20 | iaop-rust-common | README only | NEEDS_INITIALIZATION |
| 21 | iaop-event-contracts | README only | NEEDS_INITIALIZATION |
| 22 | iaop-api-contracts | README only | NEEDS_INITIALIZATION |
| 23 | iaop-test-kit | README only | NEEDS_INITIALIZATION |
| 24 | iaop-infrastructure | README only | NEEDS_INITIALIZATION |
| 25 | iaop-local-dev | README only | NEEDS_INITIALIZATION |
| 26 | iaop-observability | README only | NEEDS_INITIALIZATION |
| 27 | iaop-bpmn-definitions | README + placeholder dirs | NEEDS_INITIALIZATION |
| 28 | iaop-architecture | README + docs/ | NEEDS_INITIALIZATION |

No `EXISTING_IMPLEMENTATION`, `INCOMPATIBLE`, or `BLOCKED` repositories.
No prior standards, toolchains, or CI exist to reconcile — this is a greenfield
foundation build.

## Key decisions from discovery

- **Toolchain:** stable `1.100.0` pinned via `rust-toolchain.toml` (channel
  `1.100.0`, profile minimal + rustfmt + clippy). Edition 2024.
- **Edition/workspace:** `resolver = "3"` per-service workspaces (no cross-repo
  root workspace).
- **Dependency distribution for `iaop-*` crates:** git dependencies pinned to
  immutable commit SHAs are impractical before the first tagged release and
  complicate private-repo CI auth. Decision: template services are
  **self-contained** for the scaffold phase; `iaop-rust-common` crates are
  published internally later. Documented as a deviation; services adopt
  `iaop-*` crates via git deps pinned to tags once `v0.1.0` tags exist.
- **OpenAPI:** OpenAPI 3.1, `/api/v1/{resources}`, RFC 9457 problem details.
- **Events:** JSON Schema canonical format, versioned envelope.
- **Database:** SQLx migrations, timestamp-prefix naming
  (`YYYYMMDDHHMMSS_description.sql`), outbox + inbox templates.
- **CI/CD:** push-to-`main` only; every directly-triggerable workflow carries a
  job-level `if: github.ref == 'refs/heads/main'` guard. No PR triggers.

## Blockers

None for scaffolding. Branch protection remains unavailable on the Free org
plan (documented previously); direct pushes to `main` are the only available
merge path, which is consistent with the main-only CI policy.
