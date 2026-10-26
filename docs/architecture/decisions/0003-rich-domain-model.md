# ADR-0003: Rich Domain Model and Aggregate Design

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Domain modeling, identity, behavior, and consistency
- **Related:** ADR-0002

## Context

Garage Platform requires domain models capable of enforcing
business invariants independently of persistence, messaging,
HTTP, and dependency injection frameworks.

Anemic models with unrestricted getters and setters would
transfer business responsibility to external services.

The domain must express behavior through meaningful objects.

## Decision

Adopt a Rich Domain Model using Entities, Value Objects,
Aggregate Roots, Domain Services, and Specifications
where appropriate.

### Entities

Entities are identified by identity rather than attributes.

An Entity:

- Has a strongly typed identifier.
- Encapsulates its state.
- Exposes meaningful domain behavior.
- Protects its applicable invariants.
- Does not expose unrestricted public setters.
- May expose read-only accessors when necessary.

Entity equality must preserve identity semantics.

Entity and Aggregate Root implementations must not depend
on JPA or other persistence frameworks.

### Strongly Typed Identifiers

The common-domain module defines:

    public abstract class Id<T>

The class must:

- Accept its value through a protected constructor.
- Reject null values.
- Expose a final getValue() method.
- Preserve value immutability.
- Implement consistent equality and hashCode semantics.

Concrete identifiers extend this abstraction.

Examples:

    VehicleId extends Id<UUID>
    OwnerId extends Id<UUID>

Different concrete identifier types must not be equal
merely because their underlying values match.

Underlying values should be immutable types.

### Value Objects

Value Objects:

- Are identified by their values.
- Must be immutable unless explicitly justified.
- Validate intrinsic invariants.
- Use meaningful value equality.
- Must not expose uncontrolled mutable references.

Examples include:

- Vin
- LicensePlate
- Money
- DateRange
- OdometerValue

Not all domain concepts require a Value Object.
Primitive types may be used when no meaningful
domain abstraction is justified.

### Money

The shared Money abstraction must:

- Use BigDecimal for monetary amounts.
- Use an explicit currency.
- Reject arithmetic between incompatible currencies.
- Avoid double and float arithmetic.
- Provide immutable arithmetic operations.
- Support explicit rounding policies.
- Use numerically meaningful equality semantics.

Currency conversion requires an explicit conversion
policy and exchange rate.

Domain-specific restrictions, such as prohibiting
negative prices, belong to the relevant domain.

### Aggregate Roots

Aggregate Roots establish consistency boundaries.

An Aggregate Root:

- Owns its identity.
- Controls valid state transitions.
- Protects aggregate invariants.
- Manages internal entities when applicable.
- May record pending Domain Events.

An Aggregate Root must not:

- Execute repository operations.
- Manage infrastructure transactions.
- Publish directly to Kafka.
- Depend on Spring or Hibernate.
- Perform arbitrary external network requests.

Aggregates must remain small enough to support
reasonable consistency and concurrency boundaries.

A relational association does not automatically
imply aggregate membership.

### Behavior Over Setters

Preferred:

    vehicle.changeLicensePlate(newPlate);
    appointment.confirm();
    reservation.release();

Discouraged:

    vehicle.setLicensePlate(newPlate);
    appointment.setStatus(CONFIRMED);

Behavioral methods must validate transitions
before committing state changes.

Read-only getters are allowed when appropriate.

Internal mutable collections must be protected.

### Builders and Factories

Builders are permitted when construction complexity
justifies their existence.

Builders must never bypass domain invariants.

Domain-specific factory methods are preferred when
construction itself represents a business operation.

For example:

    Vehicle.register(...)

is more expressive than a generic constructor when
registration has domain significance.

Simple Value Objects do not require Builders merely
for consistency with other classes.

### Creation and Reconstitution

Creation and reconstitution are distinct operations.

Creation:

- Establishes a new aggregate.
- Validates creation invariants.
- May record creation events.

Reconstitution:

- Restores previously persisted state.
- Must not emit new creation events.
- Must preserve structural validity.
- May account for legitimate historical states.

Persistence adapters must use controlled
reconstitution mechanisms.

Public setters must not be introduced for JPA mapping.

### Domain Events

Aggregates may accumulate pending Domain Events.

Events must be immutable and represent meaningful
domain occurrences.

Reading pending events must not accidentally
discard them.

Events may only be cleared after successful
transactional recording, using a defined lifecycle.

Domain Events are not automatically Integration Events.

### Domain Errors

common-domain defines an abstract DomainError.

Each bounded context defines meaningful subclasses
for its own business failures.

Domain Errors must not contain HTTP-specific concepts.

Infrastructure failures should not automatically
be misclassified as domain failures.

### Specification Pattern

Specifications may encapsulate reusable and composable
business predicates.

Use Specifications when they clarify complex rules.

Do not introduce Specifications for trivial conditions
or replace an aggregate's intrinsic invariants with
external specifications.

Specifications must remain independent of persistence
unless explicitly implemented as infrastructure
query specifications.

## Alternatives

### Anemic Domain Model

Rejected because behavior and invariants would be
scattered throughout coordinating services.

### JPA Entities as Domain Aggregates

Rejected because framework requirements would
constrain domain encapsulation.

### Mandatory Builder for Every Object

Rejected because it introduces unnecessary complexity.

## Consequences

Positive:

- Strong domain encapsulation.
- Independently testable behavior.
- Clear aggregate consistency boundaries.
- Explicit business transitions.
- Framework-independent models.

Negative:

- Additional mapping code.
- More controlled construction mechanisms.
- Increased Java verbosity.
- More deliberate aggregate design.

## Verification

Tests must verify:

- Invalid states cannot be constructed through
  supported public APIs.
- Invalid transitions do not corrupt aggregate state.
- Reconstitution does not emit creation events.
- Value Objects preserve immutability.
- Identifier types remain distinct.
- Domain Core has no infrastructure dependencies.

## Final Decision

Garage Platform adopts Rich Domain Models,
strongly typed identity, controlled mutability,
and Aggregate Roots as transactional consistency
boundaries.

This decision is accepted.
