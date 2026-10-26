# ADR-0004: Kafka and Event-Driven Integration

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Asynchronous microservice communication
- **Related:** ADR-0002, ADR-0003

## Context

Garage Platform contains independently deployed
microservices with separate persistence ownership.

Services must communicate business occurrences
without directly accessing another service's database
or internal domain implementation.

The architecture intentionally adopts Event-Driven
Architecture as a primary integration mechanism.

## Decision

Apache Kafka is the primary asynchronous
integration platform.

Services must publish and consume versioned
Integration Events through explicit contracts.

### Domain Events and Integration Events

Domain Events represent occurrences within a
bounded context.

Integration Events represent externally visible,
versioned facts intended for other consumers.

They must be treated as separate concepts.

An internal Domain Event must not automatically
be serialized and published to Kafka.

A mapping boundary must transform domain occurrences
into approved integration contracts.

### Event Semantics

Integration Events should:

- Describe completed business facts.
- Use meaningful past-tense names.
- Carry a unique event identifier.
- Include a schema version.
- Include the relevant aggregate identifier.
- Include an occurrence timestamp.
- Support correlation and causation metadata.

Examples:

    VehicleRegistered
    MaintenanceCompleted
    StockReserved
    AppointmentConfirmed

Commands and events must remain distinct.

A command requests behavior.

An event describes an occurrence.

### Messaging Ownership

Each bounded context owns the meaning of events
that represent its business facts.

Consumers must not depend on another service's
Java domain classes.

Published events must follow documented
contract compatibility rules.

### Ordering

Kafka partition keys must be selected according
to business ordering requirements.

Events associated with the same aggregate may
use the aggregate identifier as partition key.

Global ordering across all aggregates must not
be assumed.

Consumers must account for reprocessing and
possible stale or out-of-order business information
where the workflow requires it.

### Message Delivery

Consumers must tolerate duplicate delivery.

Business side effects must be idempotent where
duplicate processing would be harmful.

Event publication must use reliable persistence
when coupled to committed business state.

Transactional guarantees are specified in ADR-0005.

### Failure Handling

Consumers must support:

- Bounded retry policies.
- Transient failure handling.
- Non-retryable failure classification.
- Dead-letter handling where justified.
- Operational visibility.
- Safe recovery procedures.

Dead-letter queues must not silently become
permanent storage for unresolved failures.

### Synchronous Communication

Kafka does not replace every synchronous API.

HTTP or another approved synchronous mechanism
may be used when an immediate response is required.

Avoid unnecessary chains of synchronous calls
between multiple services.

### Frontend Boundary

Frontend applications must not communicate
directly with Kafka.

Backend services may expose approved HTTP,
SSE, or WebSocket interfaces for client updates.

The frontend's transport adapters must hide
the implementation details from application logic.

### Event Contracts

Integration contracts must be explicitly versioned.

AsyncAPI may describe messaging interfaces.

The exact payload serialization technology,
schema registry, and topic naming conventions
remain separate architectural decisions.

### Security and Privacy

Events must not unnecessarily expose:

- Personal identification information.
- Private ownership documents.
- Authentication credentials.
- QR access tokens.
- Sensitive financial information.

Consumers must receive only information
appropriate for their integration purpose.

## Alternatives

### Synchronous Communication Only

Rejected because it increases temporal coupling
between independently deployed services.

### Direct Database Integration

Rejected because it violates service ownership.

### Sharing Domain Classes as Kafka Contracts

Rejected because it couples domain implementations
across bounded contexts.

## Consequences

Positive:

- Loose temporal coupling.
- Independent consumer evolution.
- Event-based read projections.
- Support for asynchronous workflows.
- Improved extensibility.

Negative:

- Eventual consistency.
- Duplicate and ordering considerations.
- Operational complexity.
- Contract version management.
- Increased observability requirements.

## Verification

Verify:

- Producer/consumer contract compatibility.
- Correct event metadata.
- Appropriate partitioning.
- Duplicate-safe processing.
- Retry and recovery behavior.
- No direct frontend Kafka access.
- No cross-service domain implementation imports.

## Final Decision

Apache Kafka is the primary asynchronous
integration mechanism.

Domain Events and Integration Events remain
separate architectural concepts.

This decision is accepted.
