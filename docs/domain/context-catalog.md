# Bounded Context Catalog and Collaboration Map

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
Formalize each candidate context's authority and integration. A bounded context is a **linguistic and consistency boundary**, not necessarily one deployable process. ADR-0015 remains Draft.

| Context | Core business capability | Authoritative data | Upstream facts / commands | Downstream published information |
| --- | --- | --- | --- | --- |
| Vehicle Registry | Identity, temporal ownership, odometer | Vehicles, ownership relationships, observations | MaintenanceCompleted, manual commands | VehicleRegistered, VehicleOdometerRecorded, ownership facts |
| Maintenance | Service order/history and due plans | Orders, records, plans, due state | Vehicle facts; stock and appointment outcomes | MaintenanceCompleted, MaintenanceDueDetected |
| Inventory | Physical part/catalog stock and reservations | Parts, stock movements, allocations | ReserveStock/ReleaseStock/ConsumeStock | StockReserved, StockReservationRejected, StockConsumed |
| Scheduling | Availability and appointment lifecycle | Calendars/appointments | Booking requests | AppointmentConfirmed/Rejected/Cancelled |
| Public Access | QR grants and authorized public projections | Grants, token hashes, projections | Authorized vehicle + service summaries | QRGrantRevoked or audit signal |
| Notifications | Reminder processing/delivery | Preferences (if owned), attempts, deliveries | Maintenance due events | NotificationDelivered/Failed (if needed) |
| Identity/Access | Login, principal, memberships/grants | TBD | User/organization operations | Membership/access changes TBD |
| Organizations | Business groups, memberships and rights | TBD | Identity + workshop registration | Organization events TBD |
| Commerce | Sales/pricing/payment | Future only | Stock availability | No V1 contract |

## Strategic integration patterns
- **Published Language:** producers publish versioned events not internal entities.
- **ACL:** consumer translates external contract into local domain input; in particular Vehicle Registry consumes `MaintenanceCompleted` without importing Maintenance Java classes.
- **Open Host Service:** authorized API exposure as contract, not remote aggregate access.
- **Customer-Supplier:** explicitly negotiate producer/consumer compatibility where downstream needs affect public schema.
- **Shared Kernel:** generic primitives only; no cross-context shared business Aggregate Root.

## Cross-context rules
1. Each mutable business fact has a single owning context.
2. Consumer replicas are projections and must carry source/version and freshness where relevant.
3. No cross-context database foreign key, table join, or write.
4. Public Access's sanitized projection does not replace Vehicle Registry/Maintenance authority.
5. Maintenance doesn't mark distributed booking complete until defined Saga conditions hold.
6. Owner change is not implicit consent to view private workshop records.
7. Business operations that happen without another context response require explicit eventual-consistency UX.

## Boundary validation exercises
- Vehicle transfer with three co-owners: does Registry own relationship update atomically?
- Owner maintenance with historic reading: Maintenance publishes; Registry records without regression.
- New workshop with valid VIN but no access grant: lookup must not expose private history.
- Stock reserved concurrently at two stores: which context/aggregate prevents over-reservation?
- Appointment confirmed but parts unavailable: who owns compensating cancellation?
- QR token revoked during projection refresh: credential validation must remain authoritative.

## Unresolved service topology
Evaluate which contexts warrant distinct production deployments at V1 versus logically isolated modules sharing a deployment. User expects Java microservice architecture, but ADR-0015 must explicitly ratify the exact service map. See `docs/architecture/open-decisions-register.md` OD-025.
