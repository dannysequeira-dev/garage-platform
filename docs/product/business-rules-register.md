# Cross-Context Business Rules Register

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Usage
Rule IDs refer to requirements, domain models and tests. **Approved** means supported by accepted ADR/constitution; **Proposed** means it needs business review.

| Rule | Statement | Owner | Status |
| --- | --- | --- | --- |
| BR-001 | Internal VehicleId must not change because VIN, plate or owner changes | Vehicle Registry | Approved architecture |
| BR-002 | Co-ownership is permitted; transfer of one relationship must not clear others | Vehicle Registry | Approved product direction |
| BR-003 | Legal Party identity is distinct from authenticated UserId | Identity/Registry | Approved direction; owner TBD |
| BR-004 | A workshop does not automatically own vehicle history it creates/reads | Security/Record owner | Approved principle |
| BR-005 | New service completion emits MaintenanceCompleted with observed odometer data | Maintenance | ADR-0016 Accepted |
| BR-006 | Registry records odometer facts asynchronously and idempotently | Vehicle Registry | ADR-0016 Accepted |
| BR-007 | Historical readings must not blindly overwrite current reading | Vehicle Registry | Approved principle |
| BR-008 | Physical odometer replacement must not be treated as ordinary regression | Vehicle Registry | Proposed detailed policy |
| BR-009 | A completed maintenance record preserves provenance and correction audit | Maintenance | Proposed |
| BR-010 | Preventive plan can trigger by distance or calendar time | Maintenance | Product direction; exact semantics TBD |
| BR-011 | Stock quantity cannot become negative due to concurrent reservations | Inventory | Proposed business invariant |
| BR-012 | Reserving does not consume stock | Inventory | Proposed |
| BR-013 | Appointment capacity must not be double-booked under concurrency | Scheduling | Proposed |
| BR-014 | Public QR access is unpredictable, scoped and revocable | Public Access | ADR-0011 Accepted |
| BR-015 | Private owner/financial information is not public by default | Public Access | Approved principle |
| BR-016 | Delivery attempts must not duplicate user reminders under repeated input | Notifications | Proposed |
| BR-017 | No direct cross-context database mutation | All | Accepted architecture |
| BR-018 | Outbox and relevant business writes share local ACID transaction | All producers | ADR-0005 Accepted |
| BR-019 | A Saga compensation is business action, not global DB rollback | Coordinating context | ADR-0006 Accepted |
| BR-020 | An authenticated login alone is insufficient for vehicle authorization | Security | Approved principle |

## Conflict protocol
When a new rule conflicts with an accepted ADR, do not implement either interpretation silently. Record the scenario, choose domain owner, evaluate alternatives, propose a new ADR or amendment, and update acceptance tests. Mark business-specific policy choices as pending until approved.
