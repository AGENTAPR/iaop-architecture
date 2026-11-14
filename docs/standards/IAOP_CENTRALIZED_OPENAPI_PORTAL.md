# IAOP Architecture Decision Record (ADR): Centralized Swagger UI & OpenAPI Developer Portal

**Status:** APPROVED  
**Date:** 2026-11-14  
**Deciders:** Principal Solution Architect, Principal API Architect, Security Architect, Backend Lead  
**Technical Scope:** `iaop-api-contracts`, `iaop-infrastructure`, All IAOP Microservices  

---

## 1. Context & Problem Statement
The IAOP ecosystem comprises 28 repositories including 19 HTTP microservices and BFFs. Previously, documentation was fragmented or non-existent, and standard industry practices of embedding separate Swagger UI instances inside every microservice introduced significant disadvantages:
1. Developers and frontend engineers had to memorize and access 19 different Swagger UI URLs.
2. Browsing documentation required all backend services to be deployed, running, and accessible, creating severe dependencies during local and frontend development.
3. Microservices incurred unnecessary memory and binary footprint from bundling Swagger UI static assets.
4. Security governance over exposed endpoints, public vs. internal classifications, and Try-It-Out execution was impossible to enforce uniformly.

---

## 2. Decision
We have established a single, centralized, enterprise-grade **OpenAPI Developer Portal** hosted within `iaop-api-contracts`.

### 2.1 Single Deployment & Origin
- One documentation host: `https://api-docs.iaop.internal` (and `http://localhost:8080/swagger-ui/`).
- Native multi-spec dropdown powered by Swagger UI's official multi-URL configuration.

### 2.2 Decoupled Availability
- OpenAPI 3.1 specifications are compiled, validated, and published into the portal repository at build/release time.
- Documentation availability is 100% independent of microservice runtime status.

### 2.3 Strict Security Defaults
- **Try It Out Disabled by Default:** `supportedSubmitMethods: []` ensures that documentation does not leak credentials or permit arbitrary queries from public browsers.
- **Remote Injection Protection:** `queryConfigEnabled: false` prevents manipulating URL parameters to query external hostnames.
- **Offline Self-Containment:** `validatorUrl: "none"` disables external schema validator calls.
- **Path Traversal Defense:** Rigorous lexical component inspection and canonicalization prevents directory traversal on spec serving routes.

### 2.4 Governance & Spec Maturity
Each service registered in `catalog/services.yaml` must define its maturity:
- `implemented_verified`: Endpoints are verified against runtime routes and unit/integration tests.
- `implemented_unverified`: Endpoints exist in code but lack certified route parity.
- `proposed_contract`: Planned design contracts (e.g. AGENA v1 proposed contracts).
- `deprecated`: Endpoints scheduled for decommissioning.
- `blocked`: Endpoints blocked by downstream dependencies.

---

## 3. Consequences & Compliance
- Microservices MUST NOT expose public Swagger UI assets. Microservices may optionally expose raw OpenAPI JSON at `/api/docs/openapi.json` for compilation tooling.
- Catalog additions and modifications must be validated using `.\tools\verify-catalog.ps1`.
- Production releases must generate SHA-256 checksum manifests using `.\tools\publish-catalog.ps1`.
