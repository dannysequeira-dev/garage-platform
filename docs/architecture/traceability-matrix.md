# V1 Candidate Requirements-to-Architecture Traceability

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
This matrix connects the current draft product scope to domain authority, design docs, contracts and test obligations. It must expand as V1 is ratified. Rows are candidate coverage, not sign-off.

| Requirement/UC | Owner | Primary design | Contract candidate | Minimum verification |
| --- | --- | --- | --- | --- |
| UC-VR-01 vehicle registration | Registry | `docs/domain/vehicle-registry/domain-blueprint.md` | POST vehicles; VehicleRegistered | Immutable ID, validation, Outbox atomicity |
| UC-VR-03 co-owner transfer | Registry/Auth | Identity/ownership model | Transfer command; ownership event | Three co-owners, conflict, access audit |
| UC-VR-05 event mileage | Registry/Maintenance | ADR-0016; state/consistency | MaintenanceCompleted | Source idempotency, out-of-order old observation |
| UC-MNT-02 owner maintenance | Maintenance | Domain blueprint | POST records; MaintenanceCompleted | Provenance, duplicate, eventual UX |
| UC-MNT-04 preventive due | Maintenance | Planning rules | MaintenanceDueDetected | OR/AND rule ratification, time/units |
| UC-INV-03 reserve stock | Inventory | Reservation and stock consistency | ReserveStock; StockReserved | Parallel oversell prevention, dedup |
| UC-SCH-01 appointment | Scheduling | Availability and time | AppointmentConfirmed/Rejected | Capacity, time zones, late response |
| UC-PUB-01 QR grant | Public Access | QR lifecycle | Grant API, public view | Revocation, field whitelist, token leak |
| UC-NTF-01 reminder delivery | Notifications | Delivery policy | MaintenanceDueDetected | Replay/opt-out/provider failure |
| UC-IAM-04 history share | Security | Authorization model | Scope grant API TBD | Foreign workshop denial |

## Architectural controls
| Control | Decision | Evidence |
| --- | --- | --- |
| Maven hexagonal isolation | ADR-0002 | ArchUnit/Maven graph plan |
| Rich aggregate consistency | ADR-0003 | Domain state tests |
| Kafka published language | ADR-0004 | AsyncAPI compatibility tests |
| Local Outbox/Inbox | ADR-0005 | DB crash/replay tests |
| Saga coordination | ADR-0006 | Compensation/recovery scenarios |
| Frontend portability | ADR-0007/0008 | TS dependency checks |
| Contracts | ADR-0009 | OpenAPI/AsyncAPI validation |
| QR security | ADR-0011 | Token scope/revoke tests |
| Event-mileage propagation | ADR-0016 | Maintenance→Registry integration tests |

## Closure rule
Each approved V1 use case must point to explicit acceptance scenarios, owned data, operations/events, authorization predicate, failure handling, NFR and test type. An unlinked requirement or unresolved blocking OD means architecture is **not yet implementation-ready**.
