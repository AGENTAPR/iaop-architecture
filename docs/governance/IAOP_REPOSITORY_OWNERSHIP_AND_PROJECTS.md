# IAOP Repository Ownership & GitHub Portfolio Governance
**Baseline:** 2026-11-14. **Status:** Proposed governance standard; verify against active ADRs. No automatic status changes or existing repository moves.

## 1. Core boundary rule
**Agent / Office / Customer are user channels, NOT three copies of insurance domains.** Keep three separate applications and BFFs, while reusing single-authority business domains. Repository naming and frontend deployment do not confer business-data ownership.

| Channel | Existing repository | Responsibilities | Must NOT own |
|---|---|---|---|
| Agent / distributor | `agena-partner-web` + `iaop-agent-bff` | Agent daily journey, self-service, team context, sales-facing composition | Agent registry, policy, customer, commission master |
| Internal staff | `agena-office-web` + `iaop-operations-bff` | Privileged workspaces: underwriting, policy operations, claims, finance, agency operations, compliance, auditing | Second copy of domain entities, privileged logic exclusively in BFF |
| Customer / policyholder | `agena-customer-web` + **proposed** `iaop-customer-bff` (create when delivery starts) | Self-service policies, claims, payments, documents, profile, support | Duplicate customer identity, policy, payment ledger |

**Frontend modules** may be organized independently within each audience application; do not create one BFF per office menu item. Domain APIs remain independently authenticated and resource-authorized.

## 2. Authoritative enterprise domain map
| Business entity/state | One system of record | Reused by |
|---|---|---|
| Authentication account, credentials, sessions at IdP | Keycloak | All channels |
| Party master and relationship roles (person, organization, policyholder, insured, beneficiary) | **TBD by ADR: Customer Master Ownership** (architecture issue #2); recommend dedicated Party/Customer context | Agent, Office, Customer |
| Agent business identity/link + code + lifecycle | `iaop-agent-service` | Agent, Office, Sales, Eligibility |
| Agency hierarchy, effective dated assignment/manager scope | `iaop-agency-service` | Agent, Office, Commission |
| Candidate/recruitment cases | `iaop-recruitment-service` | Agent, Office |
| Onboarding cases / approvals | `iaop-onboarding-service` + Workflow orchestration | Agent, Office |
| Certifications and training | `iaop-certification-service`, `iaop-learning-service` | Agent, Office |
| Selling eligibility | `iaop-eligibility-service` | Sales, Agent, Office |
| Product definition/pricing rules | Proposed Insurance Product domain; decision required | Sales, Underwriting, Policy |
| Prospect and proposal submissions | `iaop-sales-service` | Agent, Office |
| Underwriting decisions and risk evidence | Proposed Underwriting domain; decision required | Office, Policy |
| Policy contract, endorsements, renewals, coverage | Proposed authoritative Policy/Core domain; portfolio projection is **not** policy SoR | All channels |
| Billing, premium receivables, collection, payment allocation | Proposed Billing & Collections domain, separated from payment gateway adapter | Office, Customer |
| Claims adjudication/reserves/settlement | Proposed authoritative Claims domain; agent claim-assistance case is a separate journey | Office, Agent, Customer |
| Commission rules, accrual, ledger, clawback | `iaop-commission-service` | Agent, Office |
| Finance GL/subledger and reconciliation | Proposed Finance domain / integration with approved finance SoR | Office |
| Documents metadata and retention | `iaop-document-service` | All channels |
| Compliance investigations/restrictions | `iaop-compliance-service` | Office + other consumers |
| Notifications | `iaop-notification-service` | All channels |
| External system adapters | `iaop-integration-service` | Business domains |
| Long-running process execution | `iaop-workflow-service` | Business domains |
| Agent-visible policy read model | `iaop-portfolio-service` | Agent primarily |

The proposed domains are not claims of existing deployed services. Finalize owner and source-of-truth ADRs **before** creating repositories/migrations for them.

**Invariant:** no cross-service authoritative SQL access. References store stable foreign IDs, not copied mutable master records. Event-driven materialized projections are allowed only with owner, source/version, replay, reconciliation, lag and retention definitions. Read model ≠ second source of truth.

## 3. Repository organization
Keep one GitHub organization `AGENTAPR`; use **GitHub Teams** and CODEOWNERS, not new orgs, for distinct responsibilities. Preserve existing repo names and history. Three channel web repositories, two existing BFFs, and future customer BFF are experience layer. Existing `iaop-*-service` repositories are enterprise domain layer. `iaop-architecture`, `iaop-api-contracts`, `iaop-event-contracts`, `iaop-rust-common`, `iaop-test-kit`, `iaop-infrastructure`, `iaop-observability`, `iaop-local-dev`, `iaop-bpmn-definitions`, `iaop-api-gateway` are platform/governance layer.

Recommended teams: `team-experience-agent`, `team-experience-office`, `team-experience-customer`, `team-distribution`, `team-policy-underwriting`, `team-claims`, `team-billing-finance`, `team-platform-security`. Assign actual owners only once memberships and responsibilities are confirmed. Domain services should have a primary owner even when used by multiple channels.

Office application modules may include: Distribution Operations, Recruitment/Onboarding, Underwriting, Policy Administration, Billing/Collection, Claims, Commission/Finance, Compliance, Product, Customer/Party, Reporting, Platform Operations. **Modules are not automatically microservices.** Deploy office UI under one cohesive origin if desired while keeping independently maintained frontend modules where justified.

## 4. GitHub Projects v2 — organization portfolio
Prefer **one organization-level IAOP Portfolio Project** spanning all repositories with views:
- **Executive Roadmap:** milestone, target, capability and delivery confidence
- **Agent Experience:** channel=agent
- **Internal Operations:** channel=office
- **Customer Experience:** channel=customer
- **Enterprise Core:** layer=domain with core insurance contexts
- **Platform / Shared:** layer=platform
- **Security & Compliance:** gate=security
- **Integration & E2E:** blocked/verification views
- **Recently Done:** completed and evidence-backed items

Fields: `Status` (Backlog / Ready / In Progress / In Review / Blocked / Done), `Priority` (P0-P3), `Channel` (Agent / Office / Customer / Shared), `Layer` (Experience / BFF / Domain / Platform), `Bounded Context`, `Business Capability`, `Journey`, `Owner Team`, `Target Iteration`, `Dependency`, `Evidence` (link), `Release Gate`. Avoid duplicate built-in GitHub issue fields when already available at organization scope. Maintain one canonical status field, not status labels plus competing fields.

Issue naming: `[EPIC]`, `[FEATURE]`, `[TASK]`, `[SECURITY]`, `[TEST]`, `[ADR]`, `[RELEASE GATE]`. Existing label prefixes `type:`, `priority:`, `area:`, `gate:` are usable; avoid multiplying labels for every view/filter. Use Projects custom fields for Channel / Context / Layer / Status; use labels for durable categories such as `gate:security`. No `status:done` label when Project Status and issue closure already express completion.

GitHub Projects built-in automation: closing a verified issue -> Done; merged PR -> Done only for the PR item, **not automatically the parent business issue**. Issue auto-add from repos per plan limits; manually import existing issues as needed.

## 5. Evidence-based completion rule
**Implemented in code != Done.** Close a scoped issue as completed only after complete stated acceptance criteria, applicable tests, migrations, relevant contract checks, and proof of runtime behavior have passed. A parent epic closes after all required child issues and cross-service E2E acceptance, not when one endpoint exists. Partial backend implementation -> keep issue open; add evidence and remaining steps. Distinguish PR merged from feature complete, and domain capability complete from experience journey complete. If test environment or dependency is unavailable, mark Blocked / In Review rather than Done.

For new issues, use a standard body: Objective; Business Capability; Owner Domain; Consumer Channels; Scope / Non-goals; API/event/data contract; Dependencies; Security/resource scope; Acceptance Criteria; Test Evidence; Link to parent journey. Replace the existing placeholder `IAOP {domain}` in touched issue templates/bodies.

## 6. Immediate backlog ordering
P0: SSO/session/identity-link resolution, resource authorization and workspace membership, real BFF→domain delegation, removal of fake success, Agent/Agency E2E with demo identities, customer-master ADR, policy/claims source-of-truth ADRs.
P1: Office BFF privileged workflows, Agent BFF tasks/agenda, contracts, reconciliation/event reliability, performance budgets, customer-channel identity model, core-domain discovery.
P2: advanced read models, distributed cache if measured, modular office frontend scaling, additional product lines. Do not commit to Redis or new repos without measured need.

## 7. Documentation references
GitHub Docs as checked 2026-11-14: Projects/About Projects, Understanding fields, Built-in automations, Issue fields, Labels and milestones. Actual Projects v2 configuration requires authorized GraphQL project operations; ordinary issue API writes do not create/edit project views or custom fields.
