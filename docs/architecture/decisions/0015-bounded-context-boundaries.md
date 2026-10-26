# ADR-0015: Candidate Bounded Contexts and Service Boundaries

- **Status:** Draft
- **Date:** 2026-10-26
- **Scope:** Domain ownership and service decomposition
- **Related:** ADR-0002, ADR-0004

## Context

Garage Platform must support the lifecycle
of vehicles across individuals, workshops,
and organizations.

Service boundaries must reflect business
ownership and consistency requirements.

A bounded context does not automatically
need to become a separately deployed service.

The current service map is a candidate,
not an approved final decomposition.

## Proposed Bounded Contexts

### Vehicle Registry

Candidate deployment:

    vehicle-service

Owns:

- Vehicle identity.
- Vehicle identification attributes.
- Vehicle classification.
- Ownership relationships.
- Ownership history.
- Odometer history.
- Vehicle lifecycle state.

Candidate aggregates:

- Vehicle.
- Party.
- VehicleOwnership.

Final aggregate boundaries remain unresolved.

### Maintenance

Candidate deployment:

    maintenance-service

Owns:

- Maintenance records.
- Maintenance orders.
- Maintenance plans.
- Maintenance due calculations.
- Maintenance workflow state.
- Service completion.

Candidate aggregates:

- MaintenanceOrder.
- MaintenanceRecord.
- MaintenancePlan.

Maintenance may consume authoritative
odometer information from Vehicle Registry.

It must not independently overwrite
Vehicle Registry's odometer history.

### Inventory

Candidate deployment:

    inventory-service

Owns:

- Spare parts catalog.
- Stock availability.
- Inventory locations.
- Stock movement history.
- Reservations.
- Stock consumption.

Candidate aggregates:

- Part.
- StockItem.
- InventoryReservation.

Inventory owns stock correctness.

Other services must not modify
Inventory's persistence directly.

### Scheduling

Candidate deployment:

    scheduling-service

Owns:

- Appointment requests.
- Availability.
- Appointment confirmation.
- Rescheduling.
- Cancellation.

Candidate aggregates:

- Appointment.
- AvailabilitySchedule.

Scheduling remains authoritative for
appointment and availability state.

### Public Access

Candidate deployment:

    public-access-service

Owns:

- Public vehicle access grants.
- QR credential lifecycle.
- Public information projections.
- Credential revocation and rotation.

It does not own authoritative
vehicle or maintenance records.

### Notifications

Candidate deployment:

    notification-service

Owns:

- Notification delivery.
- Delivery state.
- Notification templates.
- Delivery attempts.
- Relevant delivery preferences.

Notifications must not independently
calculate authoritative maintenance due rules.

An initial implementation may operate
without a public HTTP API.

## Supporting Contexts

### Identity and Authorization

Identity and access capabilities are mandatory.

A dedicated service boundary has not
yet been approved.

Authentication, membership, and authorization
must remain distinct from vehicle ownership.

### Organizations

Organization and tenant boundaries are unresolved.

The future model must account for:

- Workshops.
- Fleets.
- Individual participants.
- Multi-organization membership.
- Cross-organization access policies.

### Commerce

Potential future capabilities include:

- Sales orders.
- Product pricing.
- Customer billing.
- Payments.
- Invoicing.

Commerce must not automatically be merged
with Inventory merely because both involve parts.

## Data Ownership Rules

Each authoritative fact has one owning context.

Candidate ownership:

| Information | Owner |
| --- | --- |
| Vehicle identity | Vehicle Registry |
| Ownership history | Vehicle Registry |
| Odometer history | Vehicle Registry |
| Maintenance history | Maintenance |
| Maintenance due | Maintenance |
| Parts and stock | Inventory |
| Stock reservations | Inventory |
| Appointments | Scheduling |
| Public QR credentials | Public Access |
| Notification delivery | Notifications |
| Sales orders | Future Commerce |

No context may directly mutate
another context's authoritative database.

Other services may maintain local projections
through approved integration mechanisms.

## Boundary Evaluation Criteria

A context should be evaluated using:

- Business language.
- Aggregate invariants.
- Transactional consistency needs.
- Data ownership.
- Event ownership.
- Operational independence.
- Security requirements.
- Integration complexity.

Service decomposition must not be driven
only by technical package organization.

## Outstanding Questions

1. Global versus tenant-scoped Vehicle identity.
2. Exact ownership aggregate boundaries.
3. Whether Party belongs inside Vehicle Registry.
4. Maintenance order versus maintenance record lifecycle.
5. Cross-organization history-sharing policy.
6. Commerce boundaries.
7. Identity and organization service boundaries.
8. Whether Notifications requires its own service initially.
9. Whether every proposed context requires an
   independent deployment from the beginning.

## Alternatives

### One Service for Every Database Entity

Rejected because entities are not necessarily
independent business capabilities.

### One Large Vehicle Management Service

Not selected as the target architecture,
though bounded contexts may share deployment
temporarily if explicitly approved.

### Proposed Context-Based Decomposition

Preferred as the current design direction,
pending validation of actual domain workflows.

## Acceptance Criteria

Before acceptance:

- Core business workflows must be mapped.
- Aggregate ownership must be reviewed.
- Cross-context events must be identified.
- Data ownership must be unambiguous.
- Service deployment boundaries must be justified.
- Authorization implications must be evaluated.

## Status

Draft.

The proposed context map is a working model,
not a final microservice deployment mandate.
