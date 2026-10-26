# ADR-0002: Modular Hexagonal Backend Architecture

- **Status:** Accepted
- **Date:** 2026-10-26
- **Decision Makers:** Project Maintainer
- **Scope:** Backend architecture, Maven modules, dependency boundaries
- **Related Decisions:** ADR-0001
- **Related Documents:**
  - `docs/architecture/constitution.md`
  - `docs/architecture/system-overview.md`

---

## 1. Context

Garage Platform uses Java microservices following:

- Domain-Driven Design (DDD).
- Hexagonal Architecture (Ports and Adapters).
- Event-Driven Architecture (EDA / EDDD).
- Rich Domain Models.
- SOLID and Clean Code principles.

Each microservice must remain independently buildable,
deployable, testable, and maintainable.

Business behavior must remain independent of technical
frameworks and infrastructure implementation details.

The architecture must provide explicit separation between:

- Domain behavior.
- Business use-case coordination.
- Inbound communication.
- Outbound communication.
- Persistence.
- Distributed messaging.
- Transactional event publication.
- Distributed workflow coordination.
- Runtime composition.

## 2. Decision

Each Java microservice SHALL use a modular Maven
architecture with explicit dependency boundaries.

The service's domain defines business behavior
and infrastructure contracts.

Infrastructure modules implement the contracts
defined by the domain-service module.

The executable container assembles the required
modules and provides framework configuration.

The domain SHALL NOT depend on infrastructure.

## 3. Maven Structure

The repository backend uses parent POMs
and Maven multi-module aggregation.

A typical service follows:

    <name>-service/
        pom.xml

        <name>-domain/
            pom.xml
            <name>-domain-core/
            <name>-domain-service/

        <name>-application/
        <name>-messaging/
        <name>-outbox/
        <name>-saga/
        <name>-dataaccess/
        <name>-container/

Each Java module must declare its dependencies
through its own pom.xml.

Aggregator POMs use packaging type `pom`.

The executable container produces the deployable
Spring Boot application artifact.

Other modules normally produce standard JARs.

Optional modules must not be introduced without
an actual architectural responsibility.

---

# Part I - Domain Layer

## 4. Domain Core

The domain-core module contains pure domain logic.

Responsibilities include:

- Entities.
- Aggregate Roots.
- Value Objects.
- Domain Events.
- Domain Errors.
- Domain invariants.
- Domain behavior.
- Factories.
- Builders when appropriate.
- Pure domain specifications.

The module MUST NOT depend on:

- Spring Framework.
- Spring Boot.
- JPA or Hibernate.
- Kafka.
- HTTP frameworks.
- Database clients.
- Infrastructure implementations.

Domain Core MAY depend on approved common-domain
primitives.

Domain behavior MUST be independently testable
without starting an application container.

## 5. Rich Domain Model

Entities and aggregates must expose behavior
rather than unrestricted state mutation.

Business operations should be expressed
through meaningful methods.

Examples:

    vehicle.changeLicensePlate(newPlate);
    maintenance.complete(completion);
    reservation.release();

Public setters must not be added merely
to simplify persistence or mapping.

Object state transitions must respect
the applicable domain invariants.

Read-only accessors are permitted when needed.

Internal mutable collections must not allow
uncontrolled external modification.

## 6. Aggregate Roots

Aggregate Roots define transactional consistency
boundaries within a domain.

An Aggregate Root is responsible for:

- Maintaining aggregate invariants.
- Controlling state transitions.
- Managing internal aggregate entities.
- Protecting the aggregate boundary.
- Recording domain events when appropriate.

An Aggregate Root is not responsible for:

- Repository operations.
- HTTP communication.
- Kafka publication.
- Database transactions.
- Infrastructure orchestration.

Aggregates must not depend on technical
persistence mechanisms.

Aggregate boundaries are defined by business
consistency requirements, not database joins.

## 7. Aggregate Construction

Creation and reconstitution are distinct.

Creation represents a new domain operation.

Reconstitution restores an existing persisted state.

Creation may register domain events.

Reconstitution must not emit creation events.

Builders and factories must enforce applicable
domain invariants.

Persistence adapters must not require public
setters on domain aggregates.

## 8. Domain Events

Domain Events represent meaningful occurrences
within the domain model.

Aggregate Roots may record pending Domain Events.

Domain Events must remain independent of Kafka.

An internal Domain Event is not automatically
a versioned Integration Event.

Event mapping and publication boundaries
are defined separately.

---

# Part II - Domain Service

## 9. Domain Service Responsibility

The domain-service module coordinates
business use cases.

This module intentionally includes responsibilities
commonly described as Application Services in DDD.

Responsibilities include:

- Coordinating domain operations.
- Loading and persisting aggregates through ports.
- Invoking aggregate behavior.
- Coordinating cross-aggregate workflows.
- Performing validation that requires external data.
- Defining transaction boundaries.
- Defining integration dependency contracts.
- Coordinating relevant domain event handling.

Intrinsic aggregate invariants must remain
inside the owning domain model.

Domain Service must not become an anemic
substitute for aggregate behavior.

## 10. Ports

Ports are contracts defined by domain-service.

Ports must express requirements of the consuming
domain rather than technical implementation details.

The standard package organization is:

    domain-service/
        ports/
            repositories/
            transactions/
            listeners/
            publishers/

Additional categories may be introduced when
required by an actual use case.

Ports must not depend on infrastructure modules.

Ports may use approved domain types and
transport-independent operation models.

## 11. Repository Ports

Repository ports define persistence operations
required by business use cases.

Example:

    public interface VehicleRepository {
        Optional<Vehicle> findById(VehicleId id);
        boolean existsByVin(Vin vin);
        void save(Vehicle vehicle);
    }

Repository contracts must be owned by
the consuming domain-service module.

Repository ports must not expose:

- JPA entities.
- EntityManager.
- Spring Data repositories.
- SQL-specific implementation details.

Repositories operate on domain aggregates
or explicitly justified domain representations.

## 12. Transaction Ports

Business use cases define their atomicity needs.

The technical transaction implementation
belongs to infrastructure.

A transaction port may expose Unit of Work semantics.

Example:

    public interface UnitOfWork {
        <T> T execute(Supplier<T> operation);
    }

The transaction port must not require
Spring-specific annotations or types.

Domain Service determines which operations
must participate in one transaction.

Infrastructure determines how the local
transaction is implemented.

Transaction coordination must not be placed
inside an event publisher merely because
some transactions contain event publication.

## 13. Publisher Ports

Publisher ports define domain-service requirements
for recording or communicating relevant events.

A publisher port must have explicit semantics.

For example:

    public interface VehicleEventRecorder {
        void record(DomainEvent event);
    }

A transactionally reliable recorder may persist
an outbox entry rather than publish directly to Kafka.

The contract must distinguish between:

- Recording an event transactionally.
- Publishing an event externally.
- Delivering an event successfully.

These operations are not equivalent.

## 14. Listener Ports

Listener ports represent application-facing
operations triggered by incoming events or messages.

They must not be tied to Kafka-specific listener APIs.

For example, an incoming message adapter may
translate an Integration Event into a command
handled by a domain-service use case.

Kafka consumers belong to infrastructure.

The domain-service module defines the
transport-independent behavior being invoked.

---

# Part III - Infrastructure Adapters

## 15. Application Module

The application module acts as an inbound
transport adapter.

It may contain:

- HTTP controllers.
- Request DTOs.
- Response DTOs.
- Transport validation.
- HTTP error mapping.
- Authentication context extraction.
- REST-specific configuration.

The application module depends on domain-service.

It must not implement authoritative business rules.

Transport DTOs must not leak into domain-core.

The module may depend on common-web components.

## 16. DataAccess Module

The dataaccess module implements persistence ports.

Responsibilities include:

- JPA entities.
- Spring Data repository declarations.
- Persistence adapters.
- Domain-to-persistence mappers.
- Persistence-to-domain mappers.
- Database queries.
- Local ACID transaction execution.
- Optimistic concurrency.
- Database constraint handling.

DataAccess must not own business use-case decisions.

DataAccess must not bypass aggregate invariants
through unrestricted domain mutation.

Persistence entities are technical representations,
not the domain aggregates themselves.

The module implements repository and transaction
ports defined in domain-service.

## 17. Messaging Module

The messaging module implements messaging adapters.

It may contain:

- Kafka consumers.
- Kafka producers where appropriate.
- Integration Event deserialization.
- Integration Event serialization.
- Message-to-command mapping.
- Message correlation propagation.
- Messaging error handling.

Messaging must not contain authoritative
business workflow rules.

Incoming messages must invoke approved
domain-service entry points.

Messaging adapters must respect idempotency
and contract versioning requirements.

## 18. Outbox Module

The outbox module provides reliable
transactional event-recording infrastructure.

It may contain:

- Outbox persistence adapters.
- Event envelope serialization.
- Domain-to-integration event mapping.
- Outbox technical configuration.
- Outbox-specific persistence models.

The module may implement publisher or event
recorder ports defined in domain-service.

The outbox implementation must participate
in the same physical local transaction as
the corresponding aggregate persistence.

Outbox must not independently start a
separate transaction for an event that requires
atomic persistence with business state.

Event delivery may be performed by CDC,
a relay, or another explicitly approved mechanism.

The transport used for eventual event delivery
must not change the domain-facing contract.

Outbox must not own Saga business transitions.

## 19. Saga Module

The saga module supports distributed
business workflow execution.

It may contain:

- Saga execution infrastructure.
- Persistent workflow state adapters.
- Timeout and retry mechanisms.
- Compensation dispatch infrastructure.
- Technical orchestration components.

Business decisions governing saga transitions
must remain in the owning domain's coordination
or domain model.

Saga infrastructure must not become a
global business rules engine.

Saga and Outbox must remain separate concerns.

Not every service requires a Saga module.

A process involving multiple service transactions
must not pretend to have one global ACID transaction.

## 20. Container Module

The container module is the Composition Root.

It is the executable Spring Boot module.

Responsibilities include:

- Application bootstrap.
- Dependency injection configuration.
- Infrastructure adapter composition.
- Runtime configuration.
- Application lifecycle configuration.
- Production packaging.
- Deployable artifact creation.

The container may depend on all required
modules belonging to its service.

The container must not implement domain
business rules.

Only the container should require the
complete runtime dependency graph.

Infrastructure implementation details must not
leak backward into domain-core or domain-service.

---

# Part IV - Dependency Architecture

## 21. Dependency Direction

The following dependencies are permitted:

    common-domain
        |
        v
    domain-core
        ^
        |
    domain-service
        ^
        |
        +----------------------------+
        |            |               |
    application   dataaccess      messaging
                     |
                  [adapter]

Additional infrastructure modules:

    outbox ------> domain-service
    saga --------> domain-service

The diagram is conceptual and does not include
every permitted shared-library dependency.

The actual module dependency graph must remain
acyclic.

## 22. Dependency Rules

The mandatory dependency rules are:

1. domain-core may depend on common-domain.

2. domain-service may depend on domain-core
   and approved shared abstractions.

3. application may depend on domain-service
   and relevant domain types.

4. dataaccess may depend on domain-service
   and domain-core.

5. messaging may depend on domain-service
   and relevant integration contracts.

6. outbox may depend on domain-service,
   domain-core, and approved technical libraries.

7. saga may depend on domain-service,
   domain-core, and approved technical libraries.

8. container may depend on every module required
   to assemble its executable application.

9. Infrastructure modules must not create
   circular dependencies.

10. Domain modules must not depend on container,
    application, messaging, outbox, saga, or dataaccess.

Infrastructure modules may share technical libraries
only through explicitly justified dependencies.

## 23. Cross-Service Dependencies

A service must not depend on another service's
internal domain, dataaccess, or container modules.

Cross-service communication must use approved
integration contracts.

Shared domain primitives must not become a mechanism
for sharing bounded-context business models.

The existence of a common Maven parent does not
permit arbitrary cross-service dependencies.

---

# Part V - Transactional Consistency

## 24. Transaction Ownership

Domain Service controls business operation sequencing.

It decides which domain operations and persistence
operations form one atomic unit.

DataAccess implements the technical transaction
mechanism.

No persistence adapter may silently commit
a partial business operation requiring atomicity.

## 25. Aggregate and Outbox Atomicity

When an operation must persist an aggregate and
record its Integration Events, both writes must
participate in the same physical database transaction.

Example conceptual flow:

    Domain Service
        |
        |-- Load aggregate
        |-- Validate domain operation
        |-- Execute aggregate behavior
        |
        |-- UnitOfWork.execute(...)
                |
                |-- Repository.save(aggregate)
                |-- EventRecorder.record(events)
                |
                |-- Local database commit

The EventRecorder implementation must not
publish directly to Kafka before commit.

An outbox record may be transformed and delivered
after the local transaction succeeds.

The UnitOfWork and Outbox adapters must use
compatible transaction infrastructure.

## 26. Transaction Scope

Pure domain validation may run before opening
a persistence transaction when appropriate.

Validation relying on concurrent database state
must account for transaction isolation and races.

Database constraints and concurrency controls
remain mandatory where applicable.

External network requests should not be held
inside local database transactions unless
specifically justified.

Cross-service workflow coordination belongs
to Saga when distributed consistency is required.

## 27. Failure Handling

Local transaction failures must not produce
successfully committed partial state.

Outbox delivery failures must not invalidate
already committed domain state.

Reliable delivery mechanisms must support
retry and idempotent processing.

Saga failures require explicit state transitions
and valid business compensation behavior.

---

# Part VI - Shared Modules

## 28. Common Domain

common-domain contains framework-independent
domain primitives.

Approved candidate abstractions include:

- Id<T>.
- Entity<I>.
- AggregateRoot<I>.
- DomainEvent.
- DomainError.
- Money.

Id<T> is an abstract class, not an interface.

Concrete identifiers must preserve strong typing.

Money uses precise decimal arithmetic and
explicit currency semantics.

Common domain abstractions must not reference
service-specific business entities.

## 29. Common Web

common-web may contain reusable HTTP concerns.

Examples:

- Consistent error response mapping.
- HTTP correlation utilities.
- Transport error conventions.
- Framework-specific web infrastructure helpers.

DomainError belongs to common-domain.

HTTP mapping of DomainError belongs outside
the pure domain.

common-web must not contain business rules.

## 30. Common Messaging

common-messaging may contain technical
messaging abstractions.

Examples:

- Correlation metadata.
- Integration Event envelopes.
- Messaging headers.
- Generic serialization conventions.

Service-specific Integration Events must not
be placed in common-messaging merely for reuse.

Public integration contracts should be
versioned through the contracts area.

---

# Part VII - Verification

## 31. Unit Testing

Domain Core tests must execute without:

- Spring context initialization.
- Database connections.
- Kafka brokers.
- HTTP infrastructure.

Domain Service tests must support fake
or in-memory port implementations.

Business rules must not require a live
infrastructure environment to be tested.

## 32. Integration Testing

Infrastructure modules require appropriate
integration tests.

Examples:

- Repository persistence.
- Persistence mapping.
- Optimistic concurrency.
- Transaction propagation.
- Aggregate and outbox atomicity.
- Kafka message handling.
- Inbox idempotency.
- Saga persistence and recovery.

Real infrastructure behavior should be tested
using suitable isolated test environments.

## 33. Architecture Testing

Architecture tests must enforce:

- No infrastructure dependencies in domain-core.
- No reverse dependencies from domain-service.
- No circular module dependencies.
- No direct cross-service persistence coupling.
- Explicit port implementation boundaries.
- Separation between domain and JPA entities.

ArchUnit may be used for Java architecture rules.

Maven dependency analysis may complement
architecture verification.

## 34. Acceptance Criteria

This ADR is implemented successfully when:

1. Domain Core compiles independently of Spring.

2. Domain Service executes use cases through
   abstract ports.

3. DataAccess implements persistence ports.

4. Application exposes HTTP without embedding
   authoritative domain rules.

5. Messaging consumes events through adapters.

6. Aggregate and outbox writes can share one
   physical database transaction.

7. Saga coordination remains separate from
   outbox event delivery.

8. Container composes the complete service.

9. Tests enforce the approved dependency graph.

10. Services remain independently deployable.

---

# Part VIII - Consequences

## 35. Positive Consequences

This architecture provides:

- Framework-independent domain models.
- Explicit dependency inversion.
- Strong separation of responsibilities.
- Infrastructure replacement flexibility.
- Independently testable business behavior.
- Controlled transaction boundaries.
- Reliable event integration.
- Independent microservice deployment.
- Clear composition and packaging responsibilities.

## 36. Negative Consequences

This architecture introduces:

- Additional Maven modules.
- More explicit interfaces.
- Increased configuration requirements.
- Additional mapping between domain and persistence.
- More complex integration testing.
- Additional responsibility for dependency governance.

These costs are accepted as intentional
architectural complexity.

Unnecessary wrappers and duplicate abstractions
must still be avoided.

## 37. Alternatives Considered

### Conventional Spring Layered Architecture

Rejected because domain behavior could become
tightly coupled to framework and persistence
implementation details.

### Single Module Per Microservice

Not selected because explicit module boundaries
are an intentional architectural requirement.

### Infrastructure-Owned Repository Contracts

Rejected because repositories must be abstractions
owned by their consuming domain-service.

### Domain Objects as JPA Entities

Rejected for the initial architecture because
persistence framework requirements must not
constrain aggregate encapsulation.

## 38. Open Questions

The following require subsequent decisions:

- Exact Maven parent POM conventions.
- Dependency and plugin version management.
- Transaction port final API.
- Domain Event extraction lifecycle.
- Integration Event mapping conventions.
- Saga persistence model and execution engine.
- Outbox relay implementation.
- Reconstitution factory conventions.
- Aggregate versioning implementation.
- Common module publication and versioning.

These open questions do not invalidate
the accepted module boundaries.

## 39. Final Decision

Garage Platform backend microservices SHALL use
modular Maven-based Hexagonal Architecture.

Domain Core owns domain invariants and behavior.

Domain Service owns business use-case coordination
and defines external dependency ports.

Infrastructure modules implement those ports.

DataAccess owns technical persistence.

Outbox owns reliable event recording.

Saga supports distributed workflow execution.

Container owns application composition and startup.

Dependencies must point toward the domain.

This decision is accepted.
