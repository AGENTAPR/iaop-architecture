# IAOP Team Ownership Model

Canonical GitHub teams (org AGENTAPR):

| Team | Slug | Owns |
|---|---|---|
| Team Agent | `team-agent` | AGENA Agent experience: `agena-partner-web`, `iaop-agent-bff`, Distribution domains (agent, agency, recruitment, onboarding, eligibility, certification, learning services) |
| Team Office | `team-office` | AGENA internal operations: `agena-office-web`, `iaop-operations-bff`, workflow/compliance/commission domains |
| Team Customer | `team-customer` | AGENA customer experience: `agena-customer-web`; customer BFF/service pending ADR |

Platform repositories (architecture, contracts, rust-common, infrastructure, test-kit, local-dev, observability, api-gateway, bpmn-definitions, and unassigned domain services) use cross-team stewardship: all three teams have read; owners per repo matrix.

Rules:
- Channel ownership != domain ownership. Frontends consume shared domain APIs; they do not duplicate them.
- Every business domain has ONE authoritative system of record.
- Cross-team changes flow through PRs reviewed by the owning team (CODEOWNERS).
- A merged PR != completed business capability; portfolio Status reflects verified acceptance.
