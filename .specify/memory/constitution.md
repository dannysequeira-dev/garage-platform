# Garage Platform Constitution

**Status:** Draft
**Scope:** Entire monorepo
**Authority:** Operational projection of the Architecture Constitution

## Core Principles

### I. Domain-Driven Design and Rich Domain Models

Business behavior MUST be modeled using Domain-Driven Design.

- Domain entities MUST expose meaningful business behavior.
- Aggregate Roots MUST protect transactional consistency boundaries.
- Business invariants MUST be enforced within their owning domain.
- Public setters MUST NOT allow unrestricted domain mutation.
- Value Objects MUST be immutable unless explicitly justified.
- Creation and reconstitution MUST be separate operations.
- Reconstitution MUST NOT generate new business events.
- Builders and factories MUST preserve domain invariants.
- Specifications MAY express reusable, complex domain predicates.

The shared Java domain foundation MUST support:

- Abstract Id<T> with constructor and getValue().
- Strongly typed concrete identifiers.
- Entity<I>.
- AggregateRoot<I>.
- DomainError.
- DomainEvent.
- Money using BigDecimal and explicit currency.

Domain models MUST NOT depend on persistence, HTTP,
messaging, or presentation frameworks.

### II. Hexagonal Architecture and Dependency Inversion

All backend services and reusable frontend application
modules MUST follow Hexagonal Architecture.

- Core domain behavior MUST remain infrastructure-independent.
- Outbound dependencies MUST be expressed through ports.
- Infrastructure adapters MUST implement the relevant ports.
- Dependency graphs MUST remain acyclic.
- Business behavior MUST NOT migrate into technical adapters.
- Application composition MUST occur at explicit Composition Roots.

Backend ports MUST reside within the corresponding
domain-service module under ports/.

Typical port categories include:

- repositories/
- transactions/
- listeners/
- publishers/

DataAccess MUST implement persistence concerns, not
business workflow decisions.

Domain Service MUST coordinate business use cases,
aggregate operations, repository calls, and transaction boundaries.

### III. Independent Services and Explicit Data Ownership

The backend MUST consist of independently deployable
Java microservices using Maven parent POMs.

- Each service MUST own its authoritative persistence.
- Services MUST NOT directly access another service's database.
- Services MUST NOT import another bounded context's
  internal domain implementation.
- Shared modules MUST NOT contain unrelated business models.
- Service boundaries MUST follow business responsibilities.
- A bounded context MUST NOT automatically become a
  separate service without architectural justification.

Proposed service boundaries remain subject to their
respective accepted architectural decisions.

The monorepo MUST NOT impose a shared runtime
deployment lifecycle on its services.

### IV. Reliable Event-Driven Integration

Apache Kafka MUST serve as the primary asynchronous
integration mechanism.

Domain Events and Integration Events MUST remain distinct.

Integration Events MUST use documented, versioned contracts.

Transactional Outbox MUST be used when business state
and reliable event publication must be coordinated.

Business persistence and Outbox recording MUST participate
in the same physical local transaction.

Consumers MUST tolerate duplicate message delivery.

Inbox or equivalent durable idempotency mechanisms MUST
protect business effects where necessary.

Saga MUST coordinate distributed business workflows
requiring multi-service transitions and compensation.

- Saga state MUST be durable when recovery is required.
- Workflow transitions MUST be explicit.
- Retries and timeouts MUST be controlled.
- Compensations MUST represent valid business operations.
- Saga and Outbox MUST remain separate responsibilities.

Kafka delivery guarantees MUST NOT be confused with
exactly-once business effects in external databases.

### V. Framework-Independent Frontend

The frontend MUST use TypeScript and pnpm Workspaces.

Next.js is the initial web application framework.

Reusable frontend Core and Application packages MUST NOT
depend on:

- React.
- Next.js.
- React Router.
- Expo.
- Electron.
- Tauri.
- Browser-specific frameworks.

Application behavior MUST remain reusable across web,
mobile, and desktop applications.

Frontend Application modules MUST define their own
external dependency ports.

HTTP, realtime, and platform-specific adapters MUST
implement those ports.

Applications MUST assemble dependencies through
Composition Roots.

The Java backend MUST remain authoritative for
business invariants, permissions, inventory integrity,
financial decisions, and durable business operations.

Frontend logic MAY coordinate interaction state,
forms, client workflows, and presentation transformations.

### VI. Shared Design System and Platform Portability

Frontend applications SHOULD share design tokens,
presentation conventions, and reusable UI components.

Shared UI MAY include:

- Buttons and inputs.
- Forms and dialogs.
- Tables and navigation.
- Typography and visual tokens.
- Accessible interaction primitives.

Framework-specific UI packages MUST remain separate
from framework-independent Core and Application modules.

Domain-specific presentation components SHOULD remain
within their owning domain.

Platform-specific UI implementations MAY differ
while sharing design tokens and behavior.

Portability MUST NOT require identical web,
mobile, and desktop component implementations.

### VII. Explicit Integration Contracts and Security

Service integrations MUST use explicit contracts.

OpenAPI is the preferred HTTP contract format.

Asynchronous events MUST have versioned schemas.

Generated transport clients MUST remain isolated
from internal domain behavior.

Authentication and authorization MUST be distinguished.

Backend authorization MUST be enforced regardless
of frontend visibility or validation.

Public QR access MUST:

- Use unpredictable credentials.
- Be scoped to approved vehicle projections.
- Support revocation.
- Prevent unrelated vehicle access.
- Exclude private information by default.

Sensitive data MUST NOT be exposed in logs,
metrics, public contracts, or integration events
without an explicit security justification.

Unresolved tenancy decisions MUST remain unresolved
until an applicable ADR is accepted.

### VIII. Automated Verification and Maintainability

Architectural constraints MUST be verifiable.

Java domain tests MUST execute without Spring,
PostgreSQL, Kafka, or external infrastructure.

TypeScript Core and Application tests MUST execute
without initializing React or Next.js.

Architecture checks MUST detect prohibited dependencies.

Integration tests MUST validate relevant:

- Transactional atomicity.
- Repository behavior.
- Outbox and Inbox guarantees.
- Saga transitions and recovery.
- Contract compatibility.
- Concurrency behavior.

Clean Code and SOLID principles SHOULD guide implementation.

Abstractions MUST have meaningful responsibilities.

Unnecessary delegation layers and speculative
functionality SHOULD NOT be introduced.

No agent may claim tests passed without executing them.

---

## Implementation Constraints

### Backend

Backend implementation MUST use Java and Maven.

Each service SHOULD follow:

    <name>-service/
        <name>-domain/
            <name>-domain-core/
            <name>-domain-service/
        <name>-application/
        <name>-messaging/
        <name>-outbox/
        <name>-saga/
        <name>-dataaccess/
        <name>-container/

Optional modules MUST only be introduced when needed.

The container module MUST be the Composition Root.

Domain Core MUST remain pure Java.

Domain Service MUST own business-use-case coordination.

Application MUST implement inbound transport concerns.

DataAccess MUST implement persistence and local
transaction mechanisms.

Messaging MUST implement messaging transport adapters.

Outbox MUST implement reliable transactional event recording.

Saga infrastructure MUST support execution and recovery
without taking ownership of unrelated business rules.

### Frontend

Frontend packages SHOULD follow:

    domains/<name>/
        core/
        application/
        adapters/
        presentation/

Applications SHOULD reside under frontend/apps/.

Reusable design system packages SHOULD reside
under frontend/shared/.

Core and Application MUST remain framework-independent.

Presentation MAY depend on UI frameworks.

Each application MUST define its own Composition Root.

Generated HTTP clients MUST remain behind adapters.

Frontend applications MUST NOT connect directly to Kafka.

### Repository

The repository MUST retain the following boundaries:

    backend/
    frontend/
    contracts/
    infra/
    docs/

Backend builds MUST remain independent of pnpm.

Frontend builds MUST remain independent of Maven.

Each deployable service or application MUST support
an independent build and release lifecycle.

Shared contracts MUST be version-controlled.

---

## Specification and Planning Gates

Every Spec Kit specification MUST identify:

1. The affected business capability or bounded context.
2. The expected behavior and user-visible outcomes.
3. Relevant business invariants.
4. Required authorization boundaries.
5. Acceptance scenarios and failure cases.

Every implementation plan MUST identify:

1. Affected Maven modules or TypeScript packages.
2. Allowed dependency directions.
3. Relevant Aggregate Roots and Value Objects.
4. Ports and required adapter implementations.
5. Transaction boundaries.
6. Integration contracts and compatibility impact.
7. Outbox, Inbox, and Saga requirements when applicable.
8. Required tests and architecture checks.
9. Operational and observability implications.
10. Documentation or ADR updates.

A plan that violates an accepted architectural constraint
MUST be revised before task generation.

Implementation tasks MUST preserve the boundaries
defined by the approved plan.

Cross-cutting architectural changes MUST reference
the applicable ADR or explicitly propose a new decision.

Draft ADRs MUST NOT be treated as approved requirements.

---

## Development Workflow

Development MUST proceed through incremental,
independently verifiable vertical slices.

Before implementation, agents MUST:

1. Read root AGENTS.md.
2. Read docs/architecture/constitution.md.
3. Read relevant accepted ADRs.
4. Read the applicable scoped constitution.
5. Identify existing contracts and domain invariants.
6. Check the proposed module dependency graph.

During implementation, agents MUST:

- Preserve domain encapsulation.
- Respect ports and adapters.
- Avoid unrelated refactoring.
- Avoid speculative services and dependencies.
- Maintain tests and contracts.
- Preserve existing business behavior unless change
  is explicitly required.

After implementation, agents MUST:

- Report modified files and modules.
- Report tests actually executed.
- Identify unverified behavior.
- Explain architectural changes.
- Document unresolved decisions.

Agents MUST NOT create commits, push changes,
deploy services, or modify remote infrastructure
without explicit authorization.

---

## Governance

This document operationalizes, but does not replace:

- docs/architecture/constitution.md
- docs/architecture/system-overview.md
- docs/architecture/decisions/
- backend/CONSTITUTION.md
- frontend/CONSTITUTION.md

The repository Architecture Constitution and accepted
ADRs govern the actual architectural decisions.

Scoped constitutions provide additional guidance
within their respective ecosystems.

Conflicts between these documents MUST be reported
and resolved rather than silently ignored.

Architectural amendments MUST include:

- Motivation.
- Affected boundaries.
- Compatibility impact.
- Alternatives considered.
- Updated documentation.
- Applicable version changes.

Constitution versions follow semantic versioning:

- MAJOR: Incompatible governance changes.
- MINOR: New binding principles or material extensions.
- PATCH: Clarifications without semantic changes.

The project constitution remains in Draft status
until explicitly ratified by the project maintainer.

Version: 0.1.0 | Ratified: Pending (Draft) | Last Amended: 2026-10-26
