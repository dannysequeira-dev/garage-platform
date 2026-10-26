# ADR-0005: Local Transactions, Outbox, and Inbox

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Local atomicity and reliable message processing
- **Related:** ADR-0002, ADR-0004

## Context

Business operations may require both:

1. Persisting authoritative business state.
2. Publishing an integration event.

Writing to PostgreSQL and publishing directly
to Kafka are not one atomic transaction by default.

A crash between those operations can create
inconsistent externally visible behavior.

Consumers may also receive duplicate messages.

## Decision

Adopt Transactional Outbox and durable Inbox
where required by reliable integration workflows.

### Local Transaction Ownership

domain-service determines the atomic operations
required by a business use case.

dataaccess implements local transaction execution.

A transaction port may be defined under:

    domain-service/ports/transactions/

The domain must not depend on Spring transactions
or database transaction managers.

### Transactional Outbox

An operation requiring reliable event publication
must atomically persist:

- The business state change.
- The corresponding outbox message.

Both writes must participate in the same
physical database transaction.

An outbox adapter must not introduce a separate
independent transaction for the event record.

### Outbox Message

A typical outbox message contains:

- Event identifier.
- Aggregate identifier.
- Event type.
- Contract version.
- Serialized payload.
- Occurrence timestamp.
- Correlation metadata.
- Required routing metadata.

Technical delivery metadata may also be stored
according to the selected relay implementation.

### Publication

The system may use:

- Change Data Capture.
- An outbox relay.
- Another explicitly approved reliable mechanism.

The exact mechanism remains undecided.

Publication occurs after successful commitment
of the relevant local state.

The producer must account for retries and
possible duplicate delivery.

### Transaction Port

A possible contract is:

    public interface UnitOfWork {
        <T> T execute(Supplier<T> operation);
    }

This is a domain-service port, not a database
or Spring Framework dependency.

The actual transaction implementation belongs
to infrastructure.

### Domain Event Lifecycle

An Aggregate Root may hold pending Domain Events.

The coordinating service may:

1. Execute aggregate behavior.
2. Open the required local transaction.
3. Persist aggregate state.
4. Record mapped integration events in Outbox.
5. Complete the local transaction.
6. Clear successfully recorded pending events.

Failures must not silently discard events.

Retries must preserve idempotency and the relevant
business operation's consistency guarantees.

### Inbox

Consumers requiring durable deduplication
must persist processed message identities.

The Inbox mechanism must ensure that recording
message completion and applying the associated
business effects are atomic when necessary.

A processed message must not be applied again
merely because Kafka redelivered it.

### Idempotency

Business idempotency must consider:

- Event identifier.
- Logical command identifier.
- Current aggregate state.
- Repeated workflow transitions.
- Duplicate external side effects.

An Inbox record alone does not guarantee safety
if external side effects occur outside its
transactional boundary.

### Error Handling

Failed local transactions must roll back their
participating writes.

Committed state must not be rolled back merely
because Kafka publication is delayed.

Publication failures require retry and
operational visibility.

Poison messages require explicit recovery
or rejection policies.

### Relationship with Saga

Outbox provides reliable event publication.

Inbox provides durable message deduplication.

Saga coordinates distributed business workflows.

These responsibilities must not be conflated.

## Alternatives

### Direct Kafka Publication Before Commit

Rejected because publication may succeed while
the database transaction fails.

### Database Commit Followed by Kafka Send

Rejected as the sole reliable mechanism because
the process may crash between operations.

### Distributed Two-Phase Commit

Not selected as the default approach because
cross-service coordination uses Saga.

## Consequences

Positive:

- Atomic business state and event recording.
- Recoverable event publication.
- Durable idempotency.
- Clear local consistency boundaries.

Negative:

- Additional persistence records.
- Relay operational requirements.
- Cleanup and retention policies.
- More complex integration testing.

## Verification

Integration tests must prove:

- Aggregate and outbox atomicity.
- Rollback removes both pending writes.
- Publication failure preserves committed state.
- Duplicate messages do not duplicate business effects.
- Shared transaction infrastructure is configured correctly.
- Recovery works after simulated failures.

## Open Decisions

- CDC versus polling relay.
- Outbox retention.
- Inbox retention.
- Event serializer and schema governance.
- Final transaction port API.

## Final Decision

Transactional Outbox and durable Inbox are adopted
for integration flows requiring reliable publication
and processing.

This decision is accepted.
