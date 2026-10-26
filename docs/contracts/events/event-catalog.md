# Integration Event Catalog - Proposed Contracts

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Principles
Cross-context events state *completed facts* and are not serialized Domain Events. The producing context owns meaning, compatibility and data minimization. Every message has stable `eventId`, `eventType`, `version`, `occurredAt`, `aggregateId` or appropriate subject, `correlationId`, optional `causationId`, and producer metadata. No sensitive bearer tokens, identity documents or unrestricted personal data in events.

| Event | Owner | Consumers (proposed) | Partition affinity | Critical payload |
| --- | --- | --- | --- | --- |
| `VehicleRegistered` | Vehicle Registry | Maintenance/Public Access | vehicleId | vehicleId, minimal descriptor |
| `VehicleIdentificationCorrected` | Registry | Public Access | vehicleId | vehicleId, approved change metadata |
| `VehicleOdometerRecorded` | Registry | Maintenance | vehicleId | vehicleId, observationId, measurement, observedAt, status |
| `OwnershipRelationshipChanged` | Registry | Authorization/Public Access | vehicleId | vehicleId, changed relationship reference, effectiveAt; no raw PII |
| `MaintenanceCompleted` | Maintenance | Vehicle Registry; selective Public Access | vehicleId | maintenanceId, vehicleId, completedAt, observed odometer, source |
| `MaintenancePlanUpdated` | Maintenance | Notifications projection if needed | planId | planId, vehicleId, version |
| `MaintenanceDueDetected` | Maintenance | Notifications | planId | planId, vehicleId, dueReason, dueFingerprint |
| `StockReserved` | Inventory | Maintenance Saga | reservationId | operationId, orderRef, reservationId, result |
| `StockReservationRejected` | Inventory | Maintenance Saga | operationId | operationId, reason code |
| `ReservationReleased` | Inventory | Maintenance Saga | reservationId | reservationId, operationId |
| `StockConsumed` | Inventory | Maintenance | reservationId | reservationId, orderRef |
| `AppointmentConfirmed` | Scheduling | Maintenance Saga | appointmentId | requestId, appointmentId, time range |
| `AppointmentRejected` | Scheduling | Maintenance Saga | requestId | requestId, reason code |
| `AppointmentCancelled` | Scheduling | Maintenance Saga | appointmentId | appointmentId, reason |
| `NotificationDeliveryUpdated` | Notifications | Operational consumers only | intentId | intentId, technical delivery status |

All events except the **decision to publish `MaintenanceCompleted` with observed odometer** are candidates. The final schema, topic name and event cardinality require review.

## MaintenanceCompleted - accepted semantic requirement
A completed maintenance business fact with immutable `maintenanceId`, `vehicleId`, `completedAt`, observed odometer (`value`, `unit`, `observedAt`) and versioned event metadata is persisted via Outbox. Vehicle Registry consumes it, checks source idempotency, records history and independently evaluates accepted current odometer. It never blindly overwrites a newer reading. No Saga is required purely for this propagation. See ADR-0016.

## Logical identity vs delivery identity
Kafka `eventId` identifies the published envelope. `(maintenanceId, sourceRevision)` identifies a logical source fact. Distinct event IDs may still represent the same business fact. An Inbox addresses technical replay; a source uniqueness policy addresses semantic duplication.

## Failure and schema constraints
Unknown event versions must not be silently interpreted as current; classify and quarantine with operational visibility. Consumer lag and reordering must not be mistaken for business-time ordering. Do not use Kafka offsets as business identity. `occuredAt`, `observedAt`, and `recordedAt` remain distinct.

## Review checklist
For each approved event specify payload schema, compatibility rules, retention, sensitive fields, ACL mapping, publisher, consumer, partition key, replay policy, retry/DLQ, idempotency, correlation and observability dashboards.
