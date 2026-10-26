# AI Agent Engineering Instructions

## 1. Purpose

This document defines the mandatory engineering guidelines for
AI agents contributing to Garage Platform.

Garage Platform is a vehicle lifecycle management system built
using Domain-Driven Design, Hexagonal Architecture, Event-Driven
Architecture, and distributed systems principles.

The project is developed incrementally, with strong emphasis on
architectural correctness, maintainability, testability, and
long-term evolution.

Agents must preserve architectural boundaries and follow
the documented engineering conventions.

## 2. Source of Truth

Before making architectural or implementation changes, consult:

1. README.md
2. docs/architecture/constitution.md
3. Relevant Architecture Decision Records (ADRs)
4. Relevant bounded-context documentation
5. Applicable module-level AGENTS.md files

Some documents may not exist during initial project setup.

Do not invent missing architectural decisions. When a required
decision is undocumented, explicitly identify the uncertainty.

The approved architecture is authoritative. Existing code
provides implementation context, but does not automatically
override documented architectural constraints.

## 3. Repository Organization

The project uses a single Git repository containing independently
buildable and deployable systems.

Expected top-level structure:

- backend/    Java microservices and backend shared modules
- frontend/   TypeScript applications and reusable packages
- contracts/  Public API and event contracts
- infra/      Infrastructure and deployment definitions
- docs/       Architecture, domain, and engineering documentation

Backend and frontend maintain independent build systems.

Backend uses Maven with parent POMs.

Frontend uses pnpm workspaces.

Do not introduce a repository-wide build framework without
an explicit architectural decision.

## 4. General Engineering Principles

All agents must follow these principles:

- Favor explicit behavior over implicit side effects.
- Apply SOLID principles where they improve the design.
- Prefer composition over unnecessary inheritance.
- Keep modules cohesive and responsibilities explicit.
- Prevent circular dependencies.
- Respect dependency inversion.
- Prefer immutable value objects.
- Avoid unrestricted mutation of domain entities.
- Avoid unnecessary abstractions and delegation layers.
- Never introduce setters merely for convenience.
- Keep business invariants within the domain.
- Design for testability without requiring infrastructure.
- Treat errors as explicit, meaningful domain concepts.
- Prefer readable code over clever implementations.
- Keep implementation complexity intentional.
- Avoid speculative functionality.

Clean Code is a guiding discipline, not a justification
for excessive abstraction.

## 5. Backend Architecture

### 5.1 Technology

Backend services use Java, Spring Boot, and Maven.

Each microservice must be independently buildable,
testable, deployable, and operable.

Microservices own their persistence models and databases.

Direct access to another microservice's database is forbidden.

Inter-service communication must occur through approved
integration contracts.

### 5.2 Module Structure

A typical service follows this structure:

<name>-service/
    pom.xml

    <name>-domain/
        <name>-domain-core/
        <name>-domain-service/

    <name>-application/
    <name>-messaging/
    <name>-outbox/
    <name>-saga/
    <name>-dataaccess/
    <name>-container/

Not every service requires every optional infrastructure module.

The final module structure must follow applicable ADRs.

### 5.3 Domain Core

The domain-core module contains:

- Entities
- Aggregate Roots
- Value Objects
- Domain Events
- Domain Errors
- Domain invariants
- Pure domain behavior
- Factories and builders where appropriate

Domain Core must not depend on:

- Spring
- JPA or Hibernate
- Kafka
- HTTP frameworks
- Persistence adapters
- Application delivery frameworks

It may depend on approved common-domain abstractions.

Domain Core must remain independently testable.

### 5.4 Rich Domain Models

Domain entities must express business behavior.

Prefer:

    vehicle.changeLicensePlate(newPlate);

Avoid unrestricted state manipulation such as:

    vehicle.setLicensePlate(newPlate);

Public setters are prohibited unless explicitly justified
by the model or an approved architectural decision.

Read-only accessors are allowed when required.

Mutable collections must not expose unrestricted
modification of internal aggregate state.

Domain behavior must enforce applicable invariants.

### 5.5 Aggregate Roots

Aggregate Roots establish consistency boundaries.

They are responsible for:

- Protecting aggregate invariants
- Controlling internal state transitions
- Coordinating entities within their aggregate boundary
- Recording domain events when applicable

Aggregate Roots must not:

- Execute database queries
- Manage transactions
- Invoke HTTP services
- Publish directly to Kafka
- Depend on persistence frameworks

Aggregate boundaries must follow transactional invariants,
not merely relational database associations.

### 5.6 Creation and Reconstitution

Creating an aggregate and reconstituting an aggregate
are distinct operations.

Creation may generate domain events.

Reconstitution restores persisted state without generating
new business events.

Factories and builders must preserve domain invariants.

Persistence-specific entities must not be used as domain
aggregate implementations.

### 5.7 Common Domain Abstractions

Shared domain primitives may include:

- Id<T>
- Entity<I>
- AggregateRoot<I>
- DomainError
- DomainEvent
- Money

Id<T> must be an abstract class with an immutable value,
a constructor, and getValue().

Concrete domains define strongly typed identifiers,
such as VehicleId and OwnerId.

Money must use precise decimal arithmetic and an explicit
currency. Monetary rounding must follow a defined policy.

Do not use floating-point arithmetic for monetary values.

Common modules must not contain business-specific entities,
rules, events, or aggregate implementations from individual
bounded contexts.

### 5.8 Domain Service

The domain-service module coordinates business use cases.

Its responsibilities include:

- Business operation coordination
- Invoking aggregate behavior
- Domain-related validations requiring external information
- Repository interaction through ports
- Transaction boundary coordination through ports
- Domain event handling and publication contracts
- Coordination of business workflows

Ports must be defined under domain-service/ports/.

Typical categories include:

- ports/repositories/
- ports/transactions/
- ports/listeners/
- ports/publishers/

Ports define contracts.

Concrete infrastructure modules implement those contracts.

Domain Service must not depend on infrastructure modules.

The domain-service naming convention includes responsibilities
commonly associated with application services in DDD.

### 5.9 Data Access

The dataaccess module implements persistence concerns:

- Repository implementations
- JPA entities
- Persistence mappings
- Database queries
- Local transaction mechanisms
- Optimistic concurrency support

DataAccess does not own business decisions.

Domain Service decides which operations are performed.

DataAccess implements how those operations are persisted.

Database constraints remain mandatory for concurrency
and data integrity.

### 5.10 Transaction Boundaries

Business use cases define their required atomic operations.

Technical transaction execution belongs to infrastructure.

Transaction ports may expose Unit of Work semantics.

Operations that require atomic persistence must participate
in the same local database transaction.

Do not use distributed database transactions as a substitute
for Saga coordination without explicit architectural approval.

## 6. Event-Driven Architecture

Apache Kafka is the primary asynchronous integration platform.

Domain Events and Integration Events are distinct concepts.

Domain Events belong to domain models.

Integration Events are externally consumed, versioned
contracts exchanged between bounded contexts.

External consumers must not depend directly on another
service's Java domain classes.

Integration contracts must be documented and versioned.

### 6.1 Transactional Outbox

Use the Transactional Outbox pattern for reliable
event publication when required.

Aggregate persistence and outbox insertion must be atomic.

Do not publish externally visible events before their
associated transaction has committed.

Direct Kafka publication is not a replacement for
transactional event persistence.

### 6.2 Inbox and Idempotency

Message consumers must account for duplicate delivery.

Use inbox or equivalent idempotency mechanisms where
message processing requires durable deduplication.

Never assume that Kafka alone guarantees exactly-once
business effects across external databases.

### 6.3 Saga

Use Saga for business workflows spanning multiple
independent transactional boundaries.

Saga implementations must define:

- Persistent workflow state
- Explicit transitions
- Idempotent execution
- Retry policies
- Timeout handling
- Failure handling
- Compensation behavior where applicable

Prefer orchestration for workflows with complex sequencing
or compensation requirements.

Simple event reactions may use choreography.

Saga and Outbox are separate architectural responsibilities.

A compensation is a business action, not necessarily
a technical rollback.

## 7. Frontend Architecture

### 7.1 Technology

Frontend uses TypeScript with pnpm workspaces.

Next.js is the initial web application framework.

Future applications may include mobile and desktop.

Frontend architecture must support framework-independent
reuse of application behavior.

### 7.2 Domain Organization

Frontend packages are organized around business domains.

A typical domain contains:

- core/
- application/
- adapters/
- presentation/

Core and application modules must remain independent
of React, Next.js, React Router, Expo, Tauri, and other
presentation frameworks.

Framework-specific dependencies belong in presentation
packages or application composition roots.

### 7.3 Backend Authority

The backend is authoritative for business invariants,
permissions, financial decisions, inventory correctness,
and durable state transitions.

Frontend packages may contain:

- Presentation transformations
- Client-side interaction models
- State coordination
- Form state and validation
- User workflow coordination
- Remote data access contracts

Do not duplicate authoritative business rules from Java.

### 7.4 Frontend Ports and Adapters

Frontend application modules define interfaces for
external dependencies.

Adapters implement HTTP, realtime communication,
storage, and platform-specific integrations.

Frontend domains must not directly depend on transport
frameworks for reusable application behavior.

Applications assemble concrete dependencies through
their composition roots.

### 7.5 Shared UI

The frontend must support reusable visual components.

Shared UI may contain:

- Design tokens
- Typography
- Form controls
- Buttons and dialogs
- Tables and navigation primitives
- Accessibility utilities
- Reusable interaction patterns

Framework-specific UI packages are allowed.

Do not force web-specific components into native
applications when their underlying platforms differ.

Business-domain presentation components should remain
within their owning domain unless reuse is justified.

### 7.6 API Contracts

Frontend/backend integration must use explicit,
versioned API contracts.

Generated clients may be used as transport implementations.

Do not expose generated API DTOs throughout the
entire frontend domain model by default.

Map transport representations to application models
at appropriate boundaries.

Frontend applications must not connect directly to Kafka.

Realtime communication must use an approved backend
interface such as SSE or WebSocket when necessary.

## 8. Testing and Verification

Tests must validate behavior and architectural boundaries.

Backend tests should cover:

- Value object invariants
- Aggregate state transitions
- Invalid domain operations
- Creation and reconstitution behavior
- Repository implementations
- Transactional atomicity
- Outbox and Inbox behavior
- Saga transitions and compensations
- Integration contract compatibility

Frontend tests should cover:

- Framework-independent application behavior
- Adapter contract implementations
- State transitions
- Component behavior
- Accessibility where applicable

Prefer unit tests for pure domain behavior.

Use integration tests when infrastructure behavior matters.

Architecture tests should enforce module dependency rules.

Never claim tests passed without executing them.

Do not invent build or test commands for modules that
have not yet been created.

## 9. Security and Data Protection

Security is mandatory across all services.

- Apply authorization on backend operations.
- Treat client input as untrusted.
- Keep credentials and secrets out of source control.
- Do not expose private ownership information publicly.
- Do not use predictable identifiers as access tokens.
- Public QR access must be scoped and revocable.
- Sensitive tokens must be stored securely.
- Do not log credentials or sensitive personal data.
- Respect tenant and organization boundaries.

Public vehicle access must expose only explicitly
authorized information.

## 10. Documentation and ADRs

Architecture documentation lives under docs/.

Significant architectural decisions must be documented
using Architecture Decision Records.

An ADR should describe:

- Context
- Decision
- Alternatives
- Consequences
- Status

Do not silently replace an accepted architectural decision.

Propose changes, explain their impact, and update the
relevant ADR when approved.

Pending decisions must not be presented as finalized.

## 11. AI Agent Working Protocol

Before implementing changes:

1. Identify the affected bounded context and modules.
2. Read relevant architecture documentation.
3. Inspect existing contracts and conventions.
4. Identify applicable invariants.
5. Respect established dependency directions.
6. Keep changes focused on the requested scope.

During implementation:

- Preserve domain encapsulation.
- Do not introduce infrastructure dependencies into Domain Core.
- Do not bypass ports with direct infrastructure access.
- Do not introduce cross-service database coupling.
- Do not add new frameworks without justification.
- Avoid unrelated refactoring.
- Keep generated code separate from domain models.
- Prefer small, reviewable changes.
- Update tests and documentation as appropriate.

After implementation:

- Report modified files.
- Explain significant design decisions.
- Report tests actually executed.
- Identify unverified behavior.
- Identify unresolved architectural questions.

Do not create commits, push branches, deploy services,
or modify remote infrastructure unless explicitly requested.

## 12. Incremental Development

Development proceeds through incremental vertical slices.

Do not generate the entire system merely because the
architecture describes its future components.

Implement only the modules and capabilities required
for the current approved scope.

Scaffolding, configuration, tests, and documentation
must evolve together.

A bounded context must not be split into additional
microservices without an explicit architectural reason.

## 13. Final Principle

Architecture exists to preserve business correctness,
maintainability, and independent evolution.

The objective is not to maximize the number of patterns,
modules, interfaces, or services.

The objective is to use each architectural mechanism
deliberately, with clearly defined responsibilities
and verifiable boundaries.
