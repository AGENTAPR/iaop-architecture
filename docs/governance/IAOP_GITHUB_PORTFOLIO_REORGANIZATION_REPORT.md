# IAOP GitHub Portfolio Reorganization Report

**Date:** 2026-11-14 · **Org:** AGENTAPR · **Portfolio project:** [IAOP — Enterprise Insurance Platform](https://github.com/orgs/AGENTAPR/projects/1) (Project #1)
**Executed by:** Devin CLI via GitHub REST + GraphQL API (`gh`)

---

## 1. Organization inventory

- **31 repositories** discovered: 28 `iaop-*` platform/domain repos + 3 `agena-*-web` channel repos (private, created earlier same day).
- **29 org Projects v2** discovered: #1 master roadmap + #3–#30 per-repository projects. Per instructions, no new per-microservice projects were created; the existing per-repo projects were left intact (deletion requires explicit authorization). Project #1 was **renamed and repurposed as the single enterprise portfolio**.
- **253 issues** across all repos inspected (250 pre-existing + 3 new channel epics; `iaop-architecture#26` was already inside the 250).
- **Labels:** normalized `type:*`, `priority:*`, `area:*`, `gate:*`, `status:*` families already existed; GitHub default labels (`bug`, `documentation`, `enhancement`, `duplicate`, `good first issue`, `help wanted`, `invalid`, `question`, `wontfix`, `accessibility`) were verified unused and **deleted (310 label instances)**. Missing recommended labels (`type:bug`, `type:documentation`, `type:architecture`, `priority:p3`, `area:frontend`) confirmed present.
- **Teams:** none exist in the org. **CODEOWNERS:** absent in all repos.

## 2. Project configuration (all verified via GraphQL)

Project #1 — `IAOP — Enterprise Insurance Platform`
Description: *"Enterprise portfolio tracking Agent Experience, Internal Operations, Customer Experience, Insurance Core Domains, Shared Platform, Security, Integration, and End-to-End Delivery."*

### Fields (16 total)

| Field | Type | State |
|---|---|---|
| Status | SINGLE_SELECT | **Rebuilt via `updateProjectV2Field`** → Backlog / Ready / In Progress / In Review / Blocked / Done (built-in default options replaced — this mutation IS supported) |
| Priority | SINGLE_SELECT | P0 Critical / P1 High / P2 Medium / P3 Low (existing) |
| Work Type, Delivery Phase, Verification Level, Risk, Release Scope | SINGLE_SELECT | existing, retained |
| **Channel** | SINGLE_SELECT | Agent / Office / Customer / Shared — created |
| **Layer** | SINGLE_SELECT | Experience / BFF / Domain / Platform / Governance — created |
| **Bounded Context** | SINGLE_SELECT | 28-option controlled vocabulary (Identity & Access … Architecture) — created; superseded and **replaced the old `Domain` field (deleted)** |
| **Journey** | SINGLE_SELECT | J1–J14 + Platform / Foundation, Channel Setup, Release / Governance — created |
| **Owner Team** | SINGLE_SELECT | 8 proposed teams + Unassigned — created |
| **Target Iteration** | SINGLE_SELECT | M1–M8 — created |
| **Release Gate** | SINGLE_SELECT | Security / Integration / Contract / Performance / Recovery / Production — created |
| **Evidence** | TEXT | created |

Not created (documented limitation/substitute): `Dependency` → native GitHub issue blocked-by dependencies used instead; `Business Capability` → covered by Bounded Context + Journey.

### Items

- **253/253 issues added** to the portfolio (235 added via `addProjectV2ItemById`; 18 pre-existing retained — no duplicates).

### Field values (verified distribution)

- **Status:** Backlog 60 · Ready 122 · In Progress 31 · In Review 33 · Blocked 2 · Done 5
- **Channel:** Shared 232 · Agent 12 · Office 8 · Customer 1
- **Layer:** Domain 154 · Platform 52 · Governance 26 · BFF 18 · Experience 3
- **Target Iteration:** M1 53 · M2 10 · M3 90 · M4 3 · M5 8 · M6 74 · M7 1 · M8 14
- **Bounded Context:** 23 of 28 options in use (unused: Identity & Access, Insurance Product, Underwriting, Billing & Collections, Finance & Accounting — no implementing repos yet)

## 3. Issue status reconciliation (evidence-based)

Method: runtime route surfaces, crate/module inventory, test files, and prior verified E2E evidence re-checked against `development` source on 2026-11-14. Scaffold services (13 domain repos exposing only template routes) were classified **Ready** (well-defined, unstarted); production-readiness epics → **Backlog**.

### Implementation-verified repos

| Repo | Evidence | Resulting statuses |
|---|---|---|
| `iaop-agent-service` @ `551b211` | 14 API routes (registry, lifecycle, appointments, identity-links, identity resolve, history), outbox/idempotency, shared `iaop-security` JWT, 14 test files | #4/#5/#6 **Done+closed**; #7/#8 In Review; #3/#9/#10/#11 In Progress; #12 Backlog |
| `iaop-agency-service` @ `266c8da` | 19 routes (org units, hierarchy ancestors/descendants, assignments, transfers, leadership), effective-dated model, live PostgreSQL E2E | #2/#3 **Done+closed**; #1/#4–#8 In Review; #9 In Progress |
| `iaop-agent-bff` @ `81a88d6` | OIDC auth-code+PKCE, session store, 3 domain HTTP clients (agent/agency/eligibility); unimplemented domains fail-closed `UNAVAILABLE` (no fake success) | #2/#3/#4 In Review; #1/#5/#6/#7/#9/#10 In Progress; #8 **Blocked** (no customer/policy service); #11 Backlog |
| `iaop-rust-common` @ `7402c86` | 13 crates incl. `iaop-security` (JWKS, Axum extractor, audience-scoped client roles), `iaop-testing` | #2/#4/#5/#7 In Review; #1/#3 In Progress; #6 Backlog |
| `iaop-test-kit` @ `3f2f408` | modules: concurrency, kafka, mock_provider, oidc, postgres, events, ids | #2/#3/#4 In Review; #1 In Progress; #5 Backlog |
| `iaop-integration-service` @ `e95a790` | real routes: operations registry, providers, callbacks, attempts, reconcile, retry | #2–#5 In Review; #1 In Progress; #6 Ready; #7 Backlog |
| `iaop-infrastructure` @ `a984190` | self-hosted-dev compose, instabox Keycloak deployment live | #3/#5/#6 In Review; #1/#2 In Progress; #4/#7/#8 Backlog |
| `iaop-local-dev` @ `32e0e73` | compose + Keycloak realm + api-docs profile | #2/#5 In Review; #1 In Progress; #3/#4/#6 Backlog |
| `iaop-observability` @ `a84f82a` | otel-collector, prometheus, alert rules YAML | #2/#4 In Review; #1 In Progress; #3/#5/#6 Backlog |
| `iaop-api-contracts` @ `29470a6` | OpenAPI portal + catalog + service contract 1.0.0 | #2/#3 In Review; #1/#4 In Progress; #5 Backlog |
| `iaop-event-contracts` @ `c0f7759` | 28 versioned event schemas + catalog | #2/#3 In Review; #1 In Progress; #4/#5 Backlog |
| `iaop-bpmn-definitions` @ `20e4655` | 1 process definition (agent-onboarding) | #1/#2 In Progress; #3–#5 Backlog |
| `iaop-api-gateway`, `iaop-operations-bff` | scaffold only | Ready (features) / Backlog (prod epics) |

### Governance / journeys (`iaop-architecture` @ `b83737d`)

- #26 domain-ownership epic → **In Progress** (governance doc committed); #23 contract freeze, #24 security hardening, #25 E2E platform → In Progress
- ADR tasks #2–#7 → **Ready** (required before core-domain repos)
- J4/J5/J13 → **In Progress** (partial backend evidence); J1–J3, J6–J12, J14 → **Backlog**; #22 release gate → **Blocked**

### Channel epics (new, aligned — not duplicated)

- `agena-partner-web#1` → Agent / Experience / J13 / M2 / **P0**
- `agena-office-web#1` → Office / Experience / J14 / M5 / P1
- `agena-customer-web#1` → Customer / Experience / M7 / P2

## 4. Issues closed as Done (with evidence comments posted)

| Issue | Evidence |
|---|---|
| iaop-agent-service#4 lifecycle state machine | routes `/activate,/suspend,/reinstate,/terminate,/submit-for-activation`; dev SHA `551b211`; 14 test files |
| iaop-agent-service#5 identity linking & codes | identity-links CRUD + `/identity-links/resolve`; `551b211` |
| iaop-agent-service#6 appointments | `/agents/{id}/appointments` + persistence + tests; `551b211` |
| iaop-agency-service#2 org restructuring | org-units hierarchy endpoints; live PG E2E; `266c8da` |
| iaop-agency-service#3 assignments & transfers | assignments/transfers/leadership + effective-dated concurrency; `266c8da` |

No epics were closed — parent journeys remain open until cross-service E2E acceptance.

## 5. Milestones

M1–M8 created in **all 31 repos** (248 milestones); every issue reassigned to its iteration milestone. Prior generic milestones (Foundation→Release) left in place but unassigned. Iteration mapping is also mirrored in the project `Target Iteration` field.

## 6. Hierarchy & dependencies

Existing ~185 native sub-issue links and 14 blocked-by deps verified intact. The 4 channel/ownership epics added to portfolio without duplicating J1–J14.

## 7. NOT configurable via API — exact manual steps required

GitHub Projects **views** and **built-in workflows** have no public GraphQL mutations (verified against docs.github.com). To complete in the UI (https://github.com/orgs/AGENTAPR/projects/1):

**Views to create** (10): Executive Roadmap (group by Target Iteration, show Status/Priority/Channel/Journey) · Agent Experience (filter Channel=Agent) · Internal Operations (Channel=Office) · Customer Experience (Channel=Customer) · Insurance Core (Layer=Domain + core contexts) · Shared Platform (Layer=Platform) · Security & Compliance (Release Gate=Security or gate:security label) · Integration & E2E (Blocked status + gate:integration) · Blocked (Status=Blocked) · Recently Completed (Status=Done, closed in last 30d).

**Workflows to enable** (Project → Workflows): auto-add issues from all 31 repos · "Item closed → Status=Done" · "Item reopened → Status=In Progress" · do NOT enable "pull request merged → Done" for issue items (PRs track separately).

## 8. Ownership model (recommendation — teams do not exist yet)

Per `docs/governance/IAOP_REPOSITORY_OWNERSHIP_AND_PROJECTS.md`: channels own experience only; `*-service` repos own their bounded context; platform repos → `team-platform-security`. Proposed teams recorded in Owner Team field. **Blocked on:** org owner confirming memberships before team creation and CODEOWNERS rollout.

## 9. Blockers & limitations

1. **Branch protection/rulesets unavailable** — org on GitHub Free; private-repo protection needs Team plan. Convention documented in `iaop-workspace/IAOP_BRANCHING_CONVENTION.md`.
2. **Views/workflows** require UI configuration (steps in §7).
3. **Owner-only PR approval** impossible without CODEOWNERS; requires teams first.
4. **5 unused Bounded Context options** await future domain repos (Customer/Party Master, Product, Underwriting, Policy, Billing, Claims, Finance) — ownership ADRs are Ready issues #2–#5 in iaop-architecture.
5. Agent BFF customer-360/policies/claims endpoints are **Blocked** on nonexistent downstream services (fail-closed `UNAVAILABLE` by design).

## 10. Verification summary

253 items · all fields populated · 5 evidence-backed closures · 248 milestones · 310 duplicate default labels removed · 0 projects duplicated · 0 issues deleted · per-repo projects untouched.
