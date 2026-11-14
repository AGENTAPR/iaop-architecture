# iaop-architecture

DDD strategic design, bounded context maps, ADRs, architecture diagrams, and technical documentation.

## Purpose

iaop-architecture is part of the **Insurance Agent Operating Platform (IAOP)**, an enterprise
insurance agent lifecycle platform. It owns the **Enterprise Architecture** bounded context
(or platform capability) within the IAOP microservices landscape.

## Bounded Context / Ownership

- **Bounded context:** Enterprise Architecture
- **Owner:** AGENTAPR
- **Category:** documentation

## Technical Stack

Documentation (Markdown, Mermaid, C4)

## Architecture Principles

- Domain-Driven Design (DDD) with explicit bounded contexts
- Hexagonal architecture (ports and adapters)
- Event-driven integration via versioned contracts
- PostgreSQL as the system of record where applicable
- Identity and access via Keycloak
- Workflow orchestration via Flowable BPM where applicable

## Responsibilities

- DDD strategic design documentation
- Bounded context maps and context relationships
- Architecture Decision Records (ADRs)
- Architecture diagrams and technical documentation

## Non-Responsibilities

- Service implementation
- Infrastructure configuration
- API and event contract definitions

## Related Repositories

- [iaop-architecture](https://github.com/AGENTAPR/iaop-architecture) - DDD strategic design, bounded context maps, ADRs
- [iaop-api-contracts](https://github.com/AGENTAPR/iaop-api-contracts) - Versioned OpenAPI contracts
- [iaop-event-contracts](https://github.com/AGENTAPR/iaop-event-contracts) - Versioned integration events
- [iaop-workflow-service](https://github.com/AGENTAPR/iaop-workflow-service) - Flowable BPM orchestration
- [iaop-local-dev](https://github.com/AGENTAPR/iaop-local-dev) - Local development environment

## Status

**INITIALIZED - NOT IMPLEMENTED**

This repository has been bootstrapped with baseline documentation and configuration only.
No business logic or service implementation exists yet.

## Development Conventions

- Default branch: `main`
- Development branch: `development` — all changes are committed and pushed here
- Squash merges preferred; merge commits and rebases disabled
- Pull requests required before merging into `main`
- Topics: iaop, insurance, insurance-agents, enterprise-architecture, architecture, adr, domain-driven-design, documentation, microservices