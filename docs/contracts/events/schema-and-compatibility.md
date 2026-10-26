# Event Envelope, Schema Evolution, and Delivery Semantics

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Proposed envelope (illustrative)
```json
{
  "eventId": "uuid",
  "eventType": "MaintenanceCompleted",
  "version": 1,
  "producer": "maintenance",
  "aggregateId": "maintenance-uuid",
  "subjectId": "vehicle-uuid",
  "occurredAt": "2026-10-25T18:00:00Z",
  "correlationId": "uuid",
  "causationId": "uuid",
  "data": {
    "maintenanceId": "maintenance-uuid",
    "vehicleId": "vehicle-uuid",
    "completedAt": "2026-10-25T18:00:00Z",
    "source": "OWNER_REPORTED",
    "odometer": {"value": "4700", "unit": "KM", "observedAt": "2026-10-25T18:00:00Z"}
  }
}
```
IDs and timestamps are examples. Decimal-as-string is a **proposed** precision-preserving wire representation; final scalar type remains OD-013.

## Source-of-truth policy
Produce versioned integration messages via transactional outbox. Domain Events remain private. API/event contracts are stored under `contracts/`; do not import another service's Java class. `contracts/asyncapi/garage-platform-events.v1.proposed.yaml` is illustrative only.

## Compatibility
- Additive optional fields with defined defaults may be compatible; removing, renaming, narrowing semantics, or changing required fields is breaking.
- Consumers must either ignore unknown additive fields safely or validate them according to policy.
- Version-aware deserialization must classify unsupported major versions explicitly.
- Plan dual-read or dual-publish transition if mixed deployed versions require it; do not silently rewrite event history.
- Events are immutable facts; corrections require new occurrences or an explicit revision model.

## Ordering
Select partition key for required per-subject ordering (often VehicleId for observed odometer). No universal total ordering. Always use business `observedAt` and source version for chronologically meaningful decisions.

## Delivery semantics
Duplicate deliveries may happen even after successful DB commit and producer acknowledgment. Inbox records and source idempotency are distinct, both important. Classify transient, permanent-contract, and business-conflict errors; define bounded retry, DLQ/review queue, durable operator remediation.

## Security and observability
Do not embed QR tokens, raw personal documents, auth cookies or unapproved PII. Propagate trace context separately from durable business correlation. Restrict topic ACLs and schema registries; monitor consumer lag, poison messages and relay delay with low-cardinality metric labels.

## Decision gate
Approve JSON Schema vs Avro/Protobuf, schema registry topology, topic taxonomy, envelope types, field precision, event timestamps, compatibility windows and retention before final contract publication.
