# IAOP CI/CD Main-Only Policy

## Trigger policy

Allowed triggers for any workflow:

- `push` with `branches: [main]` only.

Forbidden:

- `pull_request`, `pull_request_target` (any variant)
- `schedule` (cron)
- `push` to non-main branches or tags (`tags:` filters)
- `workflow_dispatch` unless explicitly justified and still guarded
- `workflow_run` chains that can execute untrusted code
- `repository_dispatch` without a job-level main guard

## Job-level guard (mandatory)

Every triggerable workflow job MUST include:

```yaml
if: github.ref == 'refs/heads/main'
```

This is defense-in-depth: even if trigger config drifts, jobs cannot run
off-main.

## Pipeline contents (per repo type)

- Rust repos: `cargo fmt --check`, `cargo clippy --all-targets
  --all-features --locked -- -D warnings`, `cargo test --locked`,
  optional contract/schema validation, Docker build.
- Contract repos: schema/OpenAPI validation scripts.
- Infra/docs repos: lint/markdown checks as applicable.

## Deployment

Merging to `main` runs CI only. **No automatic production deployment**
is configured from `main` pushes. Deployments require a separate,
explicitly authorized mechanism (documented per environment when introduced).

## Audit

`tools/audit-workflows.py` (workspace) scans all repos' workflow files
and fails on any forbidden trigger or missing job guard.
