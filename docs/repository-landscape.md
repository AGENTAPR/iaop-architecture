# IAOP Repository Landscape

Canonical inventory of all **28** Insurance Agent Operating Platform (IAOP)
repositories under the [`AGENTAPR`](https://github.com/AGENTAPR) GitHub
organization. Last verified: 2026-11-13.

All repositories are **private** with default branch **`main`**.

## A. Business Microservices (12)

Each service owns its own database (`<context>_db`). No cross-service database
access, no shared domain entities, no cross-database foreign keys.

| # | Repository | Bounded Context | Database | Responsibility |
|---|---|---|---|---|
| 01 | [iaop-recruitment-service](https://github.com/AGENTAPR/iaop-recruitment-service) | Recruitment | `recruitment_db` | Candidate recruitment, screening, referrals, interviews |
| 02 | [iaop-onboarding-service](https://github.com/AGENTAPR/iaop-onboarding-service) | Onboarding | `onboarding_db` | Onboarding, KYC, document verification, agreements |
| 03 | [iaop-agent-service](https://github.com/AGENTAPR/iaop-agent-service) | Agent Lifecycle | `agent_db` | Agent master, lifecycle, activation, suspension, termination |
| 04 | [iaop-agency-service](https://github.com/AGENTAPR/iaop-agency-service) | Agency Management | `agency_db` | Agency structure, assignments, hierarchy, transfer |
| 05 | [iaop-learning-service](https://github.com/AGENTAPR/iaop-learning-service) | Learning & Development | `learning_db` | LMS, course management, training, assessment, CPD |
| 06 | [iaop-certification-service](https://github.com/AGENTAPR/iaop-certification-service) | Certification & Licensing | `certification_db` | AAJI, LSP AAJI, certification, credentials, licensing |
| 07 | [iaop-eligibility-service](https://github.com/AGENTAPR/iaop-eligibility-service) | Eligibility & Authorization | `eligibility_db` | Selling eligibility, qualification and policy evaluation |
| 08 | [iaop-sales-service](https://github.com/AGENTAPR/iaop-sales-service) | Sales | `sales_db` | Leads, opportunities, illustrations, proposals |
| 09 | [iaop-portfolio-service](https://github.com/AGENTAPR/iaop-portfolio-service) | Portfolio & Servicing | `portfolio_db` | Policy references, agent portfolio, servicing |
| 10 | [iaop-commission-service](https://github.com/AGENTAPR/iaop-commission-service) | Commission & Incentives | `commission_db` | Commission calculation, ledger, incentive, clawback |
| 11 | [iaop-compliance-service](https://github.com/AGENTAPR/iaop-compliance-service) | Compliance | `compliance_db` | Compliance cases, restrictions, disciplinary actions |
| 12 | [iaop-performance-service](https://github.com/AGENTAPR/iaop-performance-service) | Performance Management | `performance_db` | Agent KPIs, targets, scorecards, performance analytics |

## B. Platform & Integration Services (7)

| # | Repository | Bounded Context | Responsibility |
|---|---|---|---|
| 13 | [iaop-api-gateway](https://github.com/AGENTAPR/iaop-api-gateway) | API Gateway | API gateway configuration, ingress, routing, policies |
| 14 | [iaop-agent-bff](https://github.com/AGENTAPR/iaop-agent-bff) | Agent Experience | Backend for Frontend for Agent Portal |
| 15 | [iaop-operations-bff](https://github.com/AGENTAPR/iaop-operations-bff) | Operations Experience | Backend for Frontend for Operations Console |
| 16 | [iaop-workflow-service](https://github.com/AGENTAPR/iaop-workflow-service) | Workflow Platform | Flowable BPM integration, workflow orchestration, task management |
| 17 | [iaop-integration-service](https://github.com/AGENTAPR/iaop-integration-service) | Integration Platform | Shared integration operations and reconciliation infrastructure |
| 18 | [iaop-notification-service](https://github.com/AGENTAPR/iaop-notification-service) | Notifications | Email, SMS, messaging, notification templates |
| 19 | [iaop-document-service](https://github.com/AGENTAPR/iaop-document-service) | Document Management | Document metadata, storage, upload, retrieval, scanning |

## C. Shared Libraries & Contracts (4)

| # | Repository | Bounded Context | Responsibility |
|---|---|---|---|
| 20 | [iaop-rust-common](https://github.com/AGENTAPR/iaop-rust-common) | Shared Technical Library | Logging/tracing helpers, correlation ID, security utilities, error handling, message envelopes, config loading. **No domain aggregates or business rules.** |
| 21 | [iaop-event-contracts](https://github.com/AGENTAPR/iaop-event-contracts) | Event Contracts | Versioned integration event schemas and event envelopes |
| 22 | [iaop-api-contracts](https://github.com/AGENTAPR/iaop-api-contracts) | API Contracts | Versioned OpenAPI specifications |
| 23 | [iaop-test-kit](https://github.com/AGENTAPR/iaop-test-kit) | Test Infrastructure | Integration test harnesses, testcontainers helpers, contract test utilities |

## D. Infrastructure, BPM & Architecture (5)

| # | Repository | Bounded Context | Responsibility |
|---|---|---|---|
| 24 | [iaop-infrastructure](https://github.com/AGENTAPR/iaop-infrastructure) | Platform Infrastructure | Terraform, Kubernetes, Helm, deployment infrastructure |
| 25 | [iaop-local-dev](https://github.com/AGENTAPR/iaop-local-dev) | Local Development | Docker Compose for local development (PostgreSQL, Keycloak, Flowable, event broker) |
| 26 | [iaop-observability](https://github.com/AGENTAPR/iaop-observability) | Observability | Metrics, logs, tracing, dashboards, alerts |
| 27 | [iaop-bpmn-definitions](https://github.com/AGENTAPR/iaop-bpmn-definitions) | Process Definitions | Versioned BPMN 2.0 and DMN definitions |
| 28 | [iaop-architecture](https://github.com/AGENTAPR/iaop-architecture) | Enterprise Architecture | DDD context maps, ADRs, diagrams, this document |

## Service Dependency Overview

```text
External clients
      │
      ▼
iaop-api-gateway ──► iaop-agent-bff ──► Agent-facing domain services
                └──► iaop-operations-bff ──► Back-office domain services

Recruitment ──► Onboarding ──► Agent Lifecycle ◄── Agency Management
                     │              │
                     ▼              ▼
                Document svc    Learning ──► Certification ──► Eligibility ──► Sales ──► Portfolio ──► Commission
                     │              │                                   ▲          │            │
                     └──────────────┴───────────────────────────────────┘          ▼            ▼
                                                          Compliance ──► Performance ──► (scorecards feed commission)

All domain services ──► iaop-event-contracts / iaop-api-contracts (contracts)
All services         ──► iaop-rust-common, iaop-test-kit (shared libs)
Long-running flows   ──► iaop-workflow-service ◄── iaop-bpmn-definitions
External integrations─► owned by domain services; iaop-integration-service = shared ops/reconciliation only
```

## Ownership Boundaries

- **External API ownership:** AAJI → Certification; core sales → Sales;
  policy status → Portfolio; finance payout → Commission.
  `iaop-integration-service` is limited to shared integration operations,
  operational tracking, and reconciliation — it does not own external APIs.
- **Workflow:** `iaop-workflow-service` integrates with Flowable BPM;
  `iaop-bpmn-definitions` versions process models; domain state remains
  authoritative in domain microservices.
- **`iaop-rust-common`:** technical utilities only — no domain aggregates,
  no business rules, no shared ORM entities.
- **BFFs:** aggregation/composition only — no domain logic, no direct domain
  database access.

## Implementation Priority

1. **Foundation:** iaop-architecture, iaop-api-contracts, iaop-event-contracts,
   iaop-rust-common, iaop-test-kit, iaop-local-dev, iaop-observability
2. **Platform services:** iaop-api-gateway, iaop-workflow-service,
   iaop-notification-service, iaop-document-service, iaop-integration-service,
   iaop-agent-bff, iaop-operations-bff, iaop-bpmn-definitions
3. **Core lifecycle:** iaop-agent-service, iaop-agency-service,
   iaop-onboarding-service, iaop-recruitment-service
4. **Capability services:** iaop-learning-service, iaop-certification-service,
   iaop-eligibility-service
5. **Business services:** iaop-sales-service, iaop-portfolio-service,
   iaop-commission-service, iaop-compliance-service, iaop-performance-service
6. **Infrastructure hardening:** iaop-infrastructure

## Status

All repositories are `INITIALIZED - NOT IMPLEMENTED` as of 2026-11-13.

## Engineering standards

All standards live in `docs/standards/`:

- `IAOP_ENGINEERING_STANDARDS.md` — umbrella standard
- `IAOP_RUST_WORKSPACE_STANDARD.md` — Cargo workspace layout & lints
- `IAOP_AXUM_SERVICE_TEMPLATE.md` — canonical service scaffold
- `IAOP_API_CONTRACT_STANDARD.md` — OpenAPI 3.1 rules
- `IAOP_EVENT_SCHEMA_STANDARD.md` — JSON Schema 2020-12 event rules
- `IAOP_DATABASE_MIGRATION_STANDARD.md` — SQLx migration rules
- `IAOP_CICD_MAIN_ONLY_POLICY.md` — main-only CI/CD policy
