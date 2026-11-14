# IAOP Rust Workspace Standard

## Layout

Every Rust repository is a Cargo workspace:

```
Cargo.toml              # [workspace] + [workspace.package] + [workspace.dependencies]
rust-toolchain.toml     # pinned channel + rustfmt/clippy components
Cargo.lock              # committed
crates/
  <crate>/              # library crates
src/ or crates/<bin>/   # service binary (for services)
migrations/             # SQLx migrations (services with persistence)
tests/                  # integration tests
Dockerfile
docs/
.github/workflows/main-ci.yml
```

## Workspace manifest rules

- `[workspace.package]`: `edition = "2024"`, `rust-version = "1.85"`,
  `license`, `publish = false`.
- `[workspace.dependencies]`: single source of truth for shared dep
  versions; crates reference via `dep.workspace = true`.
- Members are listed explicitly; path deps between crates allowed.

## Crate naming

`iaop-<context>` for service crates, `iaop-<tech>` for shared crates
(e.g. `iaop-errors`, `iaop-http`). Binaries keep the service repo name.

## Lint baseline

```toml
[workspace.lints.rust]
unsafe_code = "forbid"        # deny in test/infra crates where unsafe is needed
missing_docs = "warn"         # documented public API expected

[workspace.lints.clippy]
all = "warn"
pedantic = { level = "warn", priority = -1 }
```

CI runs `cargo clippy --all-targets --all-features --locked -- -D warnings`,
so warnings are errors. Generated code may use targeted `#[allow]`.

## Feature flags

Optional features must be additive. Heavy deps (e.g. OTel exporters) go
behind features with sensible no-op defaults.
