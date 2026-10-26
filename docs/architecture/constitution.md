# Architecture Constitution

**Project:** Garage Platform
**Version:** 0.1.0
**Status:** Draft
**Scope:** Entire repository

---

## 1. Purpose

This constitution establishes the architectural principles,
boundaries, and engineering constraints governing Garage Platform.

The platform is designed as a distributed vehicle lifecycle
management system with independently deployable backend services
and reusable frontend applications.

Its architecture combines:

- Domain-Driven Design (DDD)
- Hexagonal Architecture (Ports and Adapters)
- Event-Driven Architecture (EDA / EDDD)
- Clean Code and SOLID principles
- Rich Domain Models
- Distributed transaction coordination
- Framework-independent application logic

Architectural complexity is intentional when it provides
educational, operational, or long-term product value.

However, complexity must remain explicit, justified,
testable, and maintainable.

## 2. Normative Language

The terms MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY
define the strength of architectural requirements.

MUST:
A mandatory architectural constraint.

MUST NOT:
A prohibited architectural practice.

SHOULD:
A recommended practice. Exceptions require justification.

SHOULD NOT:
A discouraged practice. Exceptions require justification.

MAY:
An optional practice.

Approved Architecture Decision Records may refine this
constitution but must not silently contradict it.

Constitutional changes require explicit review and
version updates.

---

# Part I — Fundamental Principles

## 3. Domain-First Design

Business behavior MUST drive architectural decisions.

Domain models MUST represent meaningful business concepts
rather than database structures or transport payloads.

Business invariants MUST be protected by the domain.

Domain models MUST NOT depend on:

- HTTP frameworks
- Persistence frameworks
- Messaging frameworks
- Dependency injection containers
- Presentation technologies

Technical implementation details MUST remain outside
the domain model.

## 4. Rich Domain Models

Entities and Aggregate Roots MUST expose meaningful
business behavior.

Unrestricted public setters MUST NOT be introduced
for domain state manipulation.

State transitions SHOULD be expressed through
business-oriented methods.

Examples:

Preferred:

    vehicle.changeLicensePlate(newPlate);
    appointment.confirm();
    reservation.release();

Discouraged:

    vehicle.setLicensePlate(newPlate);
    appointment.setStatus(CONFIRMED);
    reservation.setReleased(true);

Read-only accessors MAY expose information required
by application services or presentation mappings.

Mutable internal collections MUST NOT be exposed in
a way that permits uncontrolled modification.

## 5. Value Objects

Value Objects MUST represent concepts identified
by their values rather than their identities.

They SHOULD be immutable.

Value Objects MUST validate their own structural
and intrinsic invariants.

Equality MUST be based on meaningful value equality.

Examples include:

- VehicleId
- Vin
- LicensePlate
- Money
- OdometerReading
- DateRange

The final aggregate ownership of these concepts
will be defined in bounded-context documentation.

## 6. Shared Domain Primitives

Common domain abstractions MUST remain independent
of individual bounded contexts.

The common-domain module MAY provide:

- Id<T>
- Entity<I>
- AggregateRoot<I>
- DomainEvent
- DomainError
- Money

Id<T> MUST be an abstract class.

It MUST encapsulate a non-null value and provide
a getValue() accessor.

Concrete identifiers SHOULD provide strong typing.

For example:

    VehicleId extends Id<UUID>
    OwnerId extends Id<UUID>

Different identifier types MUST NOT be treated
as interchangeable merely because their underlying
value types match.

## 7. Monetary Values

Money MUST use precise decimal representation.

Floating-point arithmetic MUST NOT be used
for authoritative monetary calculations.

Money MUST carry its currency explicitly.

Arithmetic between incompatible currencies MUST
be rejected unless an explicit conversion operation
and exchange-rate policy are provided.

Rounding MUST follow explicit policies.

Rounding policies MAY vary between business contexts.

Value equality SHOULD normalize insignificant
decimal scale differences.

Money MUST remain independent of persistence and
presentation frameworks.

## 8. Builders and Factories

Builders MAY be introduced for complex construction
of entities, aggregates, and Value Objects.

Builders MUST NOT bypass domain invariants.

A Builder MUST NOT provide unrestricted access to
otherwise protected internal state.

Business-oriented factories SHOULD be preferred
when object creation represents a domain operation.

Creation and reconstitution MUST be treated
as distinct operations.

Creation MAY record domain events.

Reconstitution MUST restore persisted state without
generating new business events.

Reconstitution MUST preserve structural integrity
while accounting for legitimate historical states.

---

# Part II — Aggregates and Consistency

## 9. Aggregate Roots

Aggregate Roots establish transactional
consistency boundaries.

An Aggregate Root MUST:

- Protect aggregate invariants.
- Control valid state transitions.
- Manage entities within its consistency boundary.
- Expose business-oriented behavior.
- Maintain identity and consistency.

An Aggregate Root MAY record domain events
resulting from valid state transitions.

An Aggregate Root MUST NOT:

- Access databases.
- Execute HTTP requests.
- Publish messages directly to Kafka.
- Manage infrastructure transactions.
- Depend on persistence frameworks.

Aggregate boundaries MUST be based on business
invariants, not merely relational associations.

## 10. Aggregate Persistence

Repositories SHOULD operate on Aggregate Roots.

Persistence entities MUST remain separate from
domain aggregates when using infrastructure ORMs.

Persistence mappers MUST translate between
storage representations and domain models.

Reconstitution MUST NOT emit creation events.

Domain events MUST NOT be discarded before their
required transactional persistence has succeeded.

A domain event is not automatically a public
integration event.

## 11. Concurrency

Domain validation alone MUST NOT be considered
sufficient to guarantee concurrent data integrity.

Persistence implementations MUST enforce relevant
constraints through suitable mechanisms, including:

- Unique constraints
- Foreign keys where appropriate
- Optimistic concurrency
- Transaction isolation
- Explicit locking when justified

Concurrency failures MUST be translated into
meaningful application or domain-level outcomes.

---

# Part III — Backend Architecture

## 12. Backend Technology

The backend MUST use Java and Maven.

Maven parent POMs MUST manage shared build
configuration and dependency conventions.

Backend services MUST be independently buildable
and deployable.

Each microservice MUST own its persistence model.

A service MUST NOT query or modify another
service's database directly.

Specific framework and runtime versions MUST be
documented separately from this constitution.

## 13. Service Module Organization

A backend service SHOULD follow this structure:

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

Optional infrastructure modules MAY be omitted
when not required.

Every module MUST have a clearly defined
architectural responsibility.

## 14. Domain Core

domain-core MUST contain pure domain behavior.

It MAY depend on approved common-domain primitives.

It MUST NOT depend on infrastructure modules.

It MUST NOT require Spring to execute domain tests.

## 15. Domain Service

domain-service MUST coordinate business use cases.

Its responsibilities include:

- Application-level business coordination.
- Invoking aggregate behavior.
- Domain validation requiring external information.
- Repository interaction through ports.
- Coordinating transaction boundaries.
- Coordinating domain events.
- Defining relevant external dependency contracts.

This module intentionally includes responsibilities
traditionally associated with DDD Application Services.

Business invariants intrinsic to an aggregate
MUST remain within the aggregate.

Cross-aggregate coordination MAY reside
in domain-service.

## 16. Ports and Adapters

Ports MUST be defined by the consuming
domain-service module.

Port contracts SHOULD be organized under:

    domain-service/
        ports/
            repositories/
            transactions/
            listeners/
            publishers/

Ports MUST NOT depend on infrastructure implementations.

Infrastructure adapters MUST implement the
appropriate contracts.

Concrete adapters MUST reside in their corresponding
technical modules.

Dependency direction MUST point toward the domain.

## 17. Application Module

The application module acts as an inbound adapter.

It MAY provide:

- HTTP handlers
- Request/response DTOs
- Transport validation
- Authentication-context integration
- Exception-to-HTTP mapping

It MUST NOT duplicate domain business rules.

HTTP-specific concepts MUST NOT leak into
domain-core or domain-service.

## 18. Data Access

dataaccess MUST own persistence implementation.

It MAY contain:

- JPA entities
- Repository implementations
- Persistence mappers
- Database queries
- Local transaction adapters
- Concurrency mechanisms

DataAccess MUST NOT independently decide
business workflows or domain transitions.

Application-level coordination MUST remain
in domain-service.

## 19. Transaction Management

Domain Service MUST define the required
atomicity of its business operations.

Technical transaction execution MUST be
implemented by infrastructure adapters.

A UnitOfWork or equivalent transaction port
MAY be defined in domain-service.

The implementation MUST support the required
local database transaction semantics.

Operations that persist aggregates and associated
outbox records MUST participate in the same
physical database transaction.

Distributed workflows MUST NOT rely on
cross-service ACID transactions by default.

---

# Part IV — Distributed Architecture

## 20. Event-Driven Integration

Apache Kafka is the primary asynchronous
integration mechanism.

Domain Events MUST remain distinct from
Integration Events.

Integration Events MUST be explicit,
versioned public contracts.

Internal domain implementations MUST NOT
be shared as integration dependencies
between microservices.

Events SHOULD represent completed business facts.

Commands and events MUST remain semantically distinct.

## 21. Transactional Outbox

Transactional Outbox MUST be used when reliable
publication of transactional integration events
is required.

The relevant business state and outbox record
MUST be persisted atomically.

Outbox publication MUST account for retries
and duplicate delivery.

Publishing directly to Kafka before local commit
MUST NOT be treated as equivalent to Outbox.

## 22. Inbox and Idempotency

Consumers MUST tolerate duplicate delivery
when processing externally delivered messages.

Durable idempotency SHOULD be implemented using
Inbox or equivalent mechanisms when required.

Idempotency guarantees MUST extend to relevant
business side effects.

Kafka delivery guarantees MUST NOT be assumed
to provide exactly-once effects in external databases.

## 23. Saga

Saga MUST be considered for multi-service
business processes requiring coordinated
local transactions.

A Saga SHOULD explicitly define:

- Initial state
- Valid transitions
- Successful completion
- Failure states
- Retry policies
- Timeout policies
- Compensation behavior
- Idempotency requirements
- Durable execution state

Orchestration SHOULD be preferred for complex
business processes with explicit compensations.

Choreography MAY be used for simpler reactions
to business events.

Saga and Outbox MUST remain distinct concerns.

Compensation MUST represent a valid business
operation rather than an assumed technical rollback.

Saga business rules SHOULD remain within the
owning service's business coordination layer.

Saga infrastructure MAY implement persistence,
timers, retries, and technical execution.

## 24. Integration Contracts

Synchronous APIs SHOULD use OpenAPI contracts.

Asynchronous integration SHOULD use documented,
versioned event schemas.

AsyncAPI MAY describe messaging interfaces.

Consumers MUST NOT require another service's
internal database or domain implementation.

Breaking contract changes MUST be explicitly
versioned and managed.

---

# Part V — Frontend Architecture

## 25. Frontend Technology

Frontend application logic MUST use TypeScript.

The frontend MUST support a pnpm workspace.

Next.js is the initial web application choice.

Future applications MAY target mobile or desktop
without rewriting reusable application behavior.

## 26. Framework Independence

Reusable frontend domain and application modules
MUST remain independent of presentation frameworks.

These modules MUST NOT directly depend on:

- React
- Next.js
- React Router
- Expo
- Electron
- Tauri

Framework-specific behavior MUST reside in
presentation packages or application adapters.

## 27. Frontend Domain Structure

Frontend code SHOULD be organized by domain.

A domain MAY include:

    domains/<name>/
        core/
        application/
        adapters/
        presentation/

Application modules MUST define external
dependency contracts through ports.

Adapters MUST implement the relevant contracts.

Each application MUST assemble its dependencies
through a composition root.

## 28. Backend Business Authority

The backend MUST remain authoritative for:

- Business invariants
- Durable business state
- Authorization
- Inventory integrity
- Financial calculations
- Maintenance business rules
- Ownership transitions

Frontend logic MAY implement:

- Interaction state
- View transformations
- Form coordination
- Client-side workflows
- Presentation state
- Remote data orchestration

The frontend MUST NOT become a competing
source of truth for backend business rules.

## 29. Shared UI

Frontend applications SHOULD reuse
design tokens and UI components.

Shared UI packages MAY contain:

- Buttons
- Dialogs
- Forms
- Tables
- Typography
- Navigation primitives
- Accessibility utilities
- Design tokens
- Reusable interaction patterns

Presentation-specific components MAY depend
on their respective UI frameworks.

Framework-independent domain/application modules
MUST NOT depend on UI packages.

Web and native applications MAY have distinct
component implementations.

Visual consistency MUST NOT require artificial
cross-platform coupling.

## 30. API Consumption

Backend contracts SHOULD produce typed
frontend API clients.

Generated API models SHOULD remain confined
to integration boundaries when practical.

Transport DTOs MUST NOT automatically become
frontend domain models.

Frontend applications MUST NOT communicate
directly with Kafka.

Realtime capabilities MAY use SSE or WebSocket
through defined frontend ports.

---

# Part VI — Security and Operations

## 31. Security

Authorization MUST be enforced by backend services.

Frontend permissions MUST NOT be treated as
authoritative security controls.

Tenant and organization boundaries MUST be enforced
at relevant data access and business boundaries.

Vehicle ownership MUST remain distinct from
platform user accounts and organization membership.

Sensitive information MUST NOT be exposed
through public vehicle interfaces by default.

## 32. Public QR Access

Public QR access MUST use non-predictable
access credentials.

Public access credentials MUST be revocable.

Access MUST be scoped to a specific authorized
vehicle projection.

The system MUST NOT expose unrelated vehicles,
private ownership records, or internal identifiers
beyond explicitly permitted information.

QR credential storage SHOULD use secure
token-verification practices.

## 33. Observability

Services MUST support structured logs and
correlation of distributed operations.

OpenTelemetry SHOULD provide standardized
telemetry instrumentation.

Prometheus SHOULD manage operational metrics.

Elasticsearch and Kibana MAY provide
centralized log indexing and analysis.

Zabbix MAY provide independent infrastructure
and availability monitoring.

Observability tools MUST have clear responsibilities
to avoid unnecessary duplication.

Sensitive values MUST NOT be included in
logs or metric labels.

## 34. Independent Deployment

A monorepo MUST NOT imply a monolithic deployment.

Services and applications MUST support
independent build and release processes.

Shared code changes MUST be evaluated for
their compatibility impact.

CI/CD SHOULD execute checks for affected modules
without requiring unrelated systems to rebuild.

---

# Part VII — Verification and Governance

## 35. Testing

Domain behavior MUST be independently testable.

Tests SHOULD include:

- Invariant verification
- Aggregate transitions
- Value Object behavior
- Domain error handling
- Repository integration
- Transaction boundaries
- Event persistence and delivery
- Consumer idempotency
- Saga transitions and compensation
- API contract compatibility
- Frontend application behavior

Infrastructure-dependent guarantees MUST be
validated with appropriate integration tests.

## 36. Architectural Enforcement

Architectural constraints MUST be enforced
automatically wherever practical.

Backend architecture tests SHOULD verify:

- Forbidden dependencies
- Module boundaries
- Domain independence
- Absence of cyclic dependencies

Frontend architecture checks SHOULD verify:

- Framework-independent application modules
- Import boundaries
- Package dependency direction
- Absence of circular package dependencies

Architecture documentation alone is not
sufficient enforcement.

## 37. Shared Modules

Shared modules MUST contain reusable concepts
with stable and explicit responsibilities.

They MUST NOT become repositories for unrelated
business functionality.

Domain-specific concepts SHOULD remain in their
owning bounded contexts.

Changes to shared contracts MUST consider
their impact on every consumer.

## 38. Documentation

Architectural intent MUST be documented
in version-controlled Markdown files.

Significant decisions MUST be recorded as ADRs.

An accepted ADR MUST NOT be silently overridden.

Architectural changes MUST identify:

- Motivation
- Affected contexts
- Dependency impact
- Alternatives
- Consequences
- Migration implications

## 39. AI-Assisted Development

AI agents MUST follow the architectural constitution.

Agents MUST NOT introduce architectural changes
solely for convenience.

Agents SHOULD prefer focused, reviewable changes.

Agents MUST distinguish existing implementation
from proposed architecture.

Agents MUST NOT claim unexecuted tests have passed.

Agents MUST NOT commit, push, deploy, or modify
remote environments without explicit authorization.

---

# Part VIII — Evolution

## 40. Incremental Implementation

The platform MUST evolve incrementally.

Microservice boundaries SHOULD follow meaningful
business capabilities and ownership.

A pattern MUST NOT be introduced solely
because it is available.

Advanced architectural patterns MAY be adopted
deliberately for experimentation and learning.

Such choices MUST remain documented and testable.

## 41. Open Architectural Decisions

The following decisions remain open:

- Final platform and product name.
- Initial bounded-context boundaries.
- Exact backend framework/runtime versions.
- Saga orchestration engine or custom implementation.
- Event serialization format and schema governance.
- Multi-tenancy persistence strategy.
- Frontend UI component and styling system.
- Build orchestration beyond Maven and pnpm.
- Deployment topology and orchestration platform.
- Commercial licensing and distribution strategy.

Open decisions MUST NOT be treated as approved
implementation requirements.

## 42. Constitution Amendments

Any amendment MUST include:

1. A clear explanation of the proposed change.
2. Architectural justification.
3. Compatibility and migration analysis.
4. Updated documentation.
5. An incremented constitution version.

Versioning follows semantic intent:

MAJOR:
Fundamental changes to architectural guarantees.

MINOR:
New binding principles or significant extensions.

PATCH:
Clarifications that preserve existing requirements.

## 43. Final Principle

The architecture must serve the domain.

The domain must remain understandable without
infrastructure knowledge.

Frameworks are implementation details.

Business behavior, explicit contracts, and
consistency guarantees define the system.

Complexity is permitted by design,
but accidental complexity is not.
