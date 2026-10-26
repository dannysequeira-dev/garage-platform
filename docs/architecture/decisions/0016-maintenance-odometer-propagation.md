# ADR-0016: Maintenance-to-Vehicle Odometer Propagation

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Maintenance and Vehicle Registry integration
- **Related:** ADR-0004, ADR-0005, ADR-0015

## Context

Maintenance records include odometer readings observed
when vehicle services are performed.

Vehicle Registry owns the authoritative odometer history.

Maintenance must not directly update Vehicle Registry's
database or assume ownership of odometer history.

## Decision

Maintenance SHALL publish a versioned integration event
when a maintenance operation is completed.

The MaintenanceCompleted event SHALL include:

- Unique event identifier.
- Maintenance identifier.
- Vehicle identifier.
- Completion timestamp.
- Observed odometer reading and unit.
- Effective observation timestamp.

Events SHALL be recorded through Transactional Outbox
as part of the maintenance completion transaction.

Vehicle Registry SHALL consume these events
through an inbound messaging adapter.

The adapter SHALL invoke a domain-service use case
responsible for recording odometer observations.

## Vehicle Registry Responsibilities

Vehicle Registry SHALL:

- Validate the incoming observation.
- Identify the source maintenance record.
- Prevent duplicate processing.
- Preserve historical odometer readings.
- Apply its own odometer invariants.
- Detect anomalous or conflicting observations.
- Update the current odometer projection when appropriate.

Historical readings MUST NOT blindly overwrite
more recent verified readings.

Odometer corrections and replacements SHALL follow
explicit domain behavior.

## Consistency

The integration SHALL use eventual consistency.

Maintenance completion does not require synchronous
confirmation from Vehicle Registry.

Failed processing SHALL support retries and
operational recovery.

The integration does not require a Saga unless
additional business coordination rules arise.

## Event Propagation

Vehicle Registry MAY publish VehicleOdometerRecorded
after successfully accepting a new observation.

Maintenance MAY consume this event to reevaluate
maintenance schedules.

Such processing MUST NOT emit another
MaintenanceCompleted event.

## Data Ownership

Maintenance owns the completed service record.

Vehicle Registry owns the authoritative odometer history.

Each service SHALL use its own persistence and
transaction boundaries.

No direct cross-service database writes are permitted.

## Verification

Integration tests SHALL cover:

- Successful odometer propagation.
- Duplicate event delivery.
- Historical maintenance registration.
- Invalid odometer observations.
- Conflicting readings.
- Consumer failures and retries.
- Outbox and Inbox atomicity.
- Absence of circular event processing.

## Consequences

Positive:

- Independent service evolution.
- Preserved data ownership.
- Asynchronous integration.
- Traceable odometer history.
- No synchronous cross-service dependency.

Negative:

- Eventual consistency.
- Additional consumer processing.
- Error recovery requirements.
- Need for explicit historical reading policies.

## Final Decision

Maintenance publishes completed maintenance facts.

Vehicle Registry independently processes
odometer observations and maintains
the authoritative odometer history.

This decision is accepted.
