# Garage Platform — Backend

The Garage Platform backend is a distributed system built
with Java, Maven, Domain-Driven Design, Hexagonal Architecture,
and Event-Driven Architecture.

Each microservice owns its business capabilities, persistence,
integration contracts, and deployment lifecycle.

## Architecture

The backend follows:

- Domain-Driven Design (DDD).
- Rich Domain Models.
- Hexagonal Architecture (Ports and Adapters).
- Event-Driven Domain-Driven Design (EDDD).
- SOLID and Clean Code principles.
- Transactional Outbox and Inbox.
- Saga-based distributed workflow coordination.

## Technology

- Java
- Spring Boot
- Maven with parent POMs
- PostgreSQL
- Apache Kafka
- JPA / Hibernate for persistence adapters
- Automated architecture and integration testing

Exact runtime and dependency versions will be defined
when the initial Maven project is created.

## Planned Repository Structure

    backend/
    ├── pom.xml
    │
    ├── common/
    │   ├── common-domain/
    │   ├── common-web/
    │   ├── common-messaging/
    │   └── common-observability/
    │
    └── services/
        ├── vehicle-service/
        ├── maintenance-service/
        ├── inventory-service/
        ├── scheduling-service/
        ├── public-access-service/
        └── notification-service/

Services and common modules will be introduced incrementally.

The listed service boundaries remain subject to the
bounded-context decisions documented in the architecture.

## Microservice Modules

A typical microservice contains:

    <name>-service/
    ├── pom.xml
    │
    ├── <name>-domain/
    │   ├── <name>-domain-core/
    │   └── <name>-domain-service/
    │
    ├── <name>-application/
    ├── <name>-messaging/
    ├── <name>-outbox/
    ├── <name>-saga/
    ├── <name>-dataaccess/
    └── <name>-container/

Infrastructure modules are optional when the corresponding
responsibility is not required.

### Domain Core

Contains pure domain behavior:

- Entities and Aggregate Roots.
- Value Objects.
- Domain Events.
- Domain Errors.
- Business invariants.
- Builders and factories.
- Domain specifications where appropriate.

Domain Core has no infrastructure framework dependencies.

### Domain Service

Coordinates business use cases and defines required ports.

Port contracts reside under:

    ports/
    ├── repositories/
    ├── transactions/
    ├── listeners/
    └── publishers/

Domain Service is responsible for business coordination,
while Aggregate Roots protect their own invariants.

### Application

Provides inbound HTTP adapters, request/response mapping,
and transport-specific concerns.

### DataAccess

Implements persistence ports, database mappings,
local transactions, and concurrency mechanisms.

It must not own business workflow decisions.

### Messaging

Implements asynchronous messaging adapters,
including Kafka consumers and applicable publishers.

### Outbox

Implements reliable transactional event recording.

Aggregate persistence and required outbox records must
participate in the same physical local transaction.

### Saga

Provides technical support for distributed workflow
execution, persistence, retries, and recovery.

Business transitions remain under the owning context.

### Container

Acts as the Composition Root.

Responsible for application bootstrap, dependency injection,
runtime configuration, and deployment packaging.

## Dependency Direction

Dependencies must point toward the domain.

- Domain Core depends only on approved domain primitives.
- Domain Service depends on Domain Core.
- Infrastructure implements Domain Service ports.
- Container assembles the required modules.

Circular dependencies are prohibited.

Services must not directly access another service's
database or depend on its internal domain implementation.

## Domain Modeling

The platform uses rich domain models.

Objects expose meaningful behavior instead of
unrestricted public setters.

Aggregate Roots establish transactional consistency
boundaries.

Creation and reconstitution are distinct operations.

The common-domain module will provide shared primitives
such as Id<T>, Entity<I>, AggregateRoot<I>, DomainError,
DomainEvent, and Money.

## Persistence and Consistency

Each service owns its persistence model.

Local transactions provide ACID guarantees.

Outbox and Inbox provide reliable event recording
and duplicate-safe message processing.

Saga coordinates distributed business workflows.

No global database transaction is assumed across services.

## Development

Services should be independently buildable and testable.

Pure domain tests must not require Spring, PostgreSQL,
Kafka, or other infrastructure.

Infrastructure behavior must be verified using
appropriate integration tests.

Build commands will be documented after the Maven
modules are initialized.

## Architecture Documentation

Authoritative documentation is maintained in:

- ../docs/architecture/constitution.md
- ../docs/architecture/system-overview.md
- ../docs/architecture/decisions/

Relevant ADRs include:

- ADR-0002 — Modular Hexagonal Backend
- ADR-0003 — Rich Domain Model
- ADR-0004 — Event-Driven Integration
- ADR-0005 — Outbox and Inbox
- ADR-0006 — Saga Coordination
- ADR-0009 — Contract Governance
- ADR-0014 — Architecture Enforcement

## Current Status

Architecture foundation.

Backend source modules have not yet been initialized.
