# ADR-0006: Saga-Based Distributed Workflow Coordination

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Distributed business processes
- **Related:** ADR-0004, ADR-0005

## Context

Garage Platform includes workflows spanning
multiple microservices and separate databases.

A maintenance booking may involve:

- Maintenance.
- Inventory.
- Scheduling.
- Notifications.

A single ACID transaction cannot be assumed
across these independent services.

Reliable message delivery alone does not coordinate
the business meaning of the process.

## Decision

Adopt the Saga pattern for distributed workflows
requiring coordinated local transactions,
state transitions, and compensation.

### Business Ownership

A Saga belongs to the bounded context that
owns the overall business process.

A global Saga service must not become a universal
business rules engine.

The owning domain-service defines the business
transitions and required outcomes.

Technical Saga execution may live in a separate
optional saga module within the same microservice.

### Orchestration

Orchestration is preferred for workflows with:

- Multiple dependent steps.
- Explicit compensation.
- Timeouts.
- Complex failure recovery.
- Durable intermediate state.

Choreography is permitted for simpler,
independent reactions to completed events.

Not every event-driven workflow requires a Saga.

### Saga State

A durable Saga instance should contain:

- Saga identifier.
- Business correlation identifier.
- Workflow type.
- Current state.
- Relevant participating operation identifiers.
- Version or concurrency control.
- Pending step or expected response.
- Retry metadata.
- Timeout or deadline.
- Failure details when applicable.

State changes must be idempotent.

### Example Workflow

Maintenance booking may follow:

1. Create a pending maintenance order.
2. Start a booking Saga.
3. Request inventory reservation.
4. Wait for reservation confirmation.
5. Request appointment reservation.
6. Wait for appointment confirmation.
7. Confirm the maintenance booking.
8. Mark the Saga completed.

If appointment reservation fails after parts
were reserved, the Saga requests the release
of the inventory reservation.

The precise final workflow requires a dedicated
domain design before implementation.

### Compensation

Compensation is a business operation.

A compensating action must not be treated as
an automatic technical rollback.

Compensations must account for:

- Operations already externally visible.
- Irreversible physical actions.
- Partial success.
- Repeated execution.
- Failed compensation.
- Manual recovery requirements.

For example, releasing a stock reservation is
valid compensation.

Physically installing a part cannot be undone
merely through a compensating Kafka message.

### Messaging

Saga commands, responses, and events must use
explicit integration contracts.

Participants must tolerate duplicate commands
and repeated responses.

State persistence and outgoing requests must
use reliable local transaction mechanisms.

A Saga should use Outbox and Inbox where relevant.

### Timeouts and Retries

Each asynchronous step must define appropriate:

- Expected response.
- Timeout policy.
- Retry policy.
- Idempotency behavior.
- Failure transition.
- Recovery mechanism.

Infinite unbounded retries are prohibited.

A Saga must not remain silently stuck without
operational visibility.

### User Experience

The UI must not report a distributed operation
as fully completed before its business completion
conditions are satisfied.

Relevant states may include:

- Pending.
- Processing.
- Confirmed.
- Compensating.
- Failed.
- Requires intervention.

### Engine Selection

No specific Saga engine is approved yet.

Possible approaches include:

- Custom persisted state machine.
- Workflow orchestration framework.
- Dedicated durable workflow engine.

The engine decision requires its own evaluation.

## Alternatives

### Local Transactions Only

Rejected as a complete solution for workflows
spanning independent service databases.

### Outbox Without Saga

Rejected as a substitute for business coordination.

### Two-Phase Commit Across Services

Not selected as the default distributed
consistency mechanism.

## Consequences

Positive:

- Explicit distributed workflow state.
- Recoverable multi-service operations.
- Controlled compensation.
- Better failure visibility.

Negative:

- Eventual consistency.
- Additional state and infrastructure.
- Complex failure testing.
- Explicit compensation design.

## Verification

Tests must validate:

- Successful workflow completion.
- Duplicate messages.
- Out-of-order responses.
- Partial failures.
- Timeouts and retries.
- Compensations.
- Compensation failures.
- Recovery after process restart.
- Concurrent Saga state updates.

## Final Decision

Garage Platform adopts Saga for applicable
distributed business workflows.

Business coordination belongs to the owning
bounded context.

This decision is accepted.
