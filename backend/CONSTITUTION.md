# Backend Engineering Constitution

**Project:** Garage Platform
**Scope:** backend/
**Version:** 0.1.0
**Status:** Draft

This document extends the project-wide Architecture Constitution.

The project-wide constitution and accepted ADRs remain
authoritative. This document provides backend-specific
engineering constraints.

## I. Domain Purity

Domain Core MUST remain framework-independent.

It MUST NOT depend on Spring, Hibernate, Kafka,
HTTP frameworks, database drivers, or infrastructure modules.

Domain entities MUST expose behavior rather than
unrestricted state mutation.

Aggregate Roots MUST protect their consistency
boundaries and applicable invariants.

Value Objects MUST be immutable unless explicitly
justified by the domain model.

## II. Domain Identity and Construction

Strongly typed identifiers MUST use the shared
abstract Id<T> foundation.

Entities MUST preserve identity semantics.

Aggregate Roots MUST NOT perform persistence operations.

Builders and factories MUST preserve domain invariants.

Creation and reconstitution MUST remain distinct.

Reconstitution MUST NOT produce new business events.

Money MUST use precise decimal arithmetic and
explicit currency semantics.

## III. Hexagonal Dependency Boundaries

Infrastructure dependencies MUST point toward
the domain-defined contracts.

Domain Service MUST own use-case coordination.

Ports MUST be defined within domain-service/ports/.

Infrastructure modules MUST implement those ports.

Repository interfaces MUST NOT expose JPA entities
or persistence-framework implementation details.

DataAccess MUST NOT own business workflow decisions.

## IV. Transactional Consistency

Domain Service MUST define required atomicity.

DataAccess MUST provide local transaction implementations.

Aggregate persistence and required Outbox writes
MUST share the same physical transaction.

Database constraints MUST complement domain validation
where concurrency or data integrity requires them.

Distributed transactions MUST be coordinated through
approved business workflows rather than assuming
cross-service ACID transactions.

## V. Event-Driven Integration

Domain Events MUST remain independent of Kafka.

Integration Events MUST use versioned contracts.

Transactional Outbox MUST support reliable publication.

Consumers MUST handle duplicate delivery.

Saga workflows MUST define durable state,
valid transitions, retries, and compensation.

Saga and Outbox MUST remain separate responsibilities.

## VI. Service Independence

Each microservice MUST own its persistence model.

Services MUST NOT query another service's database.

Services MUST NOT import another bounded context's
internal domain implementation as an integration contract.

Services MUST be independently buildable and deployable.

## VII. Maven and Module Structure

Java projects MUST use Maven with parent POMs.

Each module MUST explicitly declare its dependencies.

Module dependency graphs MUST remain acyclic.

The executable container MUST act as the
Composition Root.

The container MUST NOT become a location for
business rules.

## VIII. Verification

Domain Core MUST be testable without Spring.

Domain Service MUST support isolated testing
using in-memory or fake port implementations.

Infrastructure guarantees MUST be verified
through appropriate integration tests.

Architecture checks MUST prevent forbidden
dependency directions.

No test may be reported as successful
unless it was actually executed.

## Governance

This document extends:

- docs/architecture/constitution.md
- docs/architecture/decisions/0002-modular-hexagonal-backend.md
- docs/architecture/decisions/0003-rich-domain-model.md
- docs/architecture/decisions/0004-event-driven-integration.md
- docs/architecture/decisions/0005-transactional-outbox-inbox.md
- docs/architecture/decisions/0006-saga-coordination.md

Changes affecting approved architectural guarantees
MUST follow the project amendment and ADR process.

Unresolved technical decisions MUST NOT be silently
promoted into mandatory implementation requirements.
