# Ubiquitous Language - Cross-Context Glossary

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
Use one term per meaning *inside each bounded context*; equivalent words in separate contexts may differ legitimately. Document semantic translations in Anti-Corruption Layers (ACLs).

| Term | Definition / invariants | Owner or scope | Not synonymous with |
| --- | --- | --- | --- |
| Vehicle | Stable system identity for a physical vehicle | Vehicle Registry | VIN, plate, ownership claim |
| VehicleId | Immutable typed internal identity | Registry | VIN, license plate |
| VIN | Manufacturer identification attribute, potentially missing/correctable | Registry | VehicleId |
| LicensePlate | Jurisdiction-scoped registration identifier | Registry | VehicleId |
| Party | Subject that can participate in legal/business relations | Identity/Party TBD | Authenticated user |
| User | Authenticated application principal | Identity | Legal owner |
| Organization | Workshop/fleet/business participant | Organizations TBD | Individual Vehicle owner |
| Membership | Role/access relationship between User and Organization | Organizations | Ownership relation |
| Ownership | Temporal legal/claimed relationship of Party to Vehicle | Registry | Access grant |
| AccessGrant | Explicit authorization to operate or view resource | Security | Ownership |
| OdometerObservation | Attributed reading observed at a particular time | Registry | Current accepted odometer |
| OdometerEpoch | Segment associated with a physical meter's lifecycle | Registry | Historical observation |
| CurrentOdometer | Evaluated projection based on accepted observations | Registry | Maximum-ever numeric reading |
| MaintenanceOrder | Planned/requested workflow of work | Maintenance | Completed service fact |
| MaintenanceRecord | Historical account of work performed, with provenance | Maintenance | Appointment |
| MaintenancePlan | Preventive policy tied to time and/or distance | Maintenance | Work order |
| DueCondition | Result of evaluating maintenance requirements | Maintenance | Notification delivery |
| Part | Catalog identity and description | Inventory | Physical stock unit |
| StockMovement | Audited increment/decrement/transfer/adjustment | Inventory | Reservation |
| StockReservation | Temporary committed allocation of available stock | Inventory | Consumption |
| Consumption | Irreversible business fact of stock used | Inventory | Reservation |
| Appointment | Reserved/confirmed time and resource allocation | Scheduling | Maintenance order |
| QRGrant | Bearer credential permission for a limited public view | Public Access | User session |
| PublicProjection | Sanitized approved vehicle facts | Public Access | Full aggregate |
| DeliveryAttempt | Single provider send attempt | Notifications | Business reminder identity |
| DomainEvent | Internal fact in owning domain | Each context | IntegrationEvent |
| IntegrationEvent | Public versioned cross-context fact | Producer | Internal Java class |
| OutboxMessage | Locally committed intent to publish integration message | Producer infra | Saga |
| InboxReceipt | Durable record of message processing | Consumer infra | Domain event |
| Saga | Durable coordination of distributed business steps | Owning context | Global transaction |

## Time vocabulary
`occurredAt` = when business fact occurred; `observedAt` = when measurement was taken; `recordedAt` = when service persisted it; `effectiveFrom` and `effectiveToExclusive` = applicability interval; `publishedAt` = technical emission. Never substitute ingestion time for business effective time.

## Money and quantities
Monetary amount = decimal + Currency; FX requires explicit rate. Distance value = nonnegative measurement + unit; converting KM to MI requires precision and policy. Stock quantity is unit-specific; do not silently combine 'each' and 'box'.

## Naming check
Prefer `MaintenanceCompleted` for final service fact, `VehicleOdometerRecorded` for accepted observation; events should state facts in past tense. `ReserveStock` is a command, `StockReserved` an event. Terms remain candidates until schemas are approved.
