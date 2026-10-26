# Garage Platform — Product Vision and Scope

**Version:** 0.1.0
**Status:** Draft
**Date:** 2026-10-26
**Scope:** Product vision, capabilities, and proposed V1 boundaries

---

## 1. Executive Summary

Garage Platform is a vehicle lifecycle management
platform designed for individuals, workshops,
and organizations.

Its purpose is to provide a structured,
traceable, and extensible record of vehicle
identity, ownership, maintenance, mileage,
appointments, and related service operations.

The platform connects the vehicle owner's
experience with the operational needs
of automotive service providers.

Garage Platform is not intended to become
a generic ERP or e-commerce system.

Its primary responsibility is vehicle lifecycle
management and the business processes
directly associated with it.

The system is designed for eventual commercial
use while remaining suitable for personal use.

---

## 2. Product Vision

Enable every vehicle to have a coherent,
traceable, and useful lifecycle record,
regardless of how many owners, workshops,
or service providers interact with it.

Vehicle information should remain meaningful
over time without assuming that one person
or organization permanently owns all its data.

The platform should help users understand:

- What vehicle they are managing.
- Who is associated with the vehicle.
- What maintenance has been performed.
- What maintenance is due.
- Which services are scheduled.
- Which parts are required.
- What information may be shared publicly.
- Where historical information originated.

The system must preserve data ownership,
authorization boundaries, and historical
traceability throughout these operations.

---

## 3. Product Mission

Provide a reliable vehicle management
experience that combines:

1. Vehicle identification.
2. Ownership relationship management.
3. Maintenance history.
4. Preventive maintenance planning.
5. Service appointments.
6. Spare parts and inventory operations.
7. Scoped public QR access.
8. Notifications and reminders.

These capabilities must work together
without requiring shared ownership
of their internal data models.

---

## 4. Problems Being Solved

### 4.1 Fragmented Vehicle History

Vehicle maintenance information is frequently
distributed across:

- Paper invoices.
- Personal notes.
- Workshop systems.
- Messaging applications.
- Spreadsheets.
- Individual service records.

This fragmentation makes it difficult
to reconstruct a vehicle's history.

### 4.2 Maintenance Planning

Owners may struggle to determine:

- When the next service is due.
- Which maintenance was completed.
- Whether mileage or elapsed time
  triggers the next maintenance.
- What actions should be prioritized.

### 4.3 Service Coordination

Workshops may need to coordinate:

- Service requests.
- Appointments.
- Available inventory.
- Service completion.
- Customer communication.

These operations should remain consistent
without requiring one giant domain model.

### 4.4 Ownership Changes

A vehicle may:

- Change owners.
- Have simultaneous owners.
- Be maintained by multiple workshops.
- Be associated with different organizations.

Its identity must remain stable.

Ownership changes must not automatically
expose unrelated private service documents.

### 4.5 Controlled Information Sharing

Owners and authorized participants may
want to expose limited vehicle information
without granting access to private records.

Public QR access should provide
a secure, revocable mechanism
for this limited information sharing.

---

## 5. Product Principles

### Vehicle Identity Is Permanent

A VehicleId identifies the system's
representation of a vehicle.

It is independent of VIN, license plate,
owner, and organization.

The final global-versus-tenant identity
strategy remains subject to ADR-0010.

### History Is Valuable

Historical information must not be
silently overwritten or discarded
during ordinary business operations.

Corrections must preserve appropriate
traceability.

### Access Is Explicit

The existence of a relationship does not
automatically grant unrestricted access.

Vehicle ownership, account identity,
organization membership, and public access
are separate concepts.

### Services Have Clear Responsibilities

Each bounded context is responsible
for its authoritative business information.

Other contexts interact through
approved contracts.

### Maintenance Can Be Self-Reported

The platform should support maintenance
recorded by the vehicle owner,
as well as maintenance performed
by authorized service providers.

The origin and trust level of service
records must be distinguishable.

### Technology Serves the Product

The architecture must support
the required business behavior.

Technical complexity must not introduce
features that have no product justification.

---

# 6. Target Users

## 6.1 Individual Vehicle Owner

A person who manages one or more vehicles.

Typical goals:

- Register vehicles.
- Review maintenance history.
- Record personal maintenance.
- Track current and historical mileage.
- Understand upcoming maintenance.
- Schedule workshop services.
- Manage public vehicle information.

A vehicle owner may also be an enthusiast
who performs maintenance personally.

## 6.2 Workshop Operator

A person authorized to perform workshop tasks.

Typical goals:

- Access assigned vehicle information.
- Review authorized service history.
- Create and manage maintenance work.
- Record service details.
- Use or reserve spare parts.
- Complete service orders.

The workshop must not automatically receive
access to every historical private record.

## 6.3 Workshop Administrator

A user responsible for workshop operations.

Typical goals:

- Manage organization members.
- Configure applicable permissions.
- Manage service operations.
- Review appointments.
- Supervise inventory.
- Review relevant operational information.

The exact administrative permissions
remain subject to the authorization design.

## 6.4 Vehicle Co-Owner

A Party participating in an ownership
relationship with one or more vehicles.

A vehicle may have multiple co-owners.

A co-owner does not automatically possess
an authenticated platform account.

The relationship between legal ownership
and application permissions must be explicit.

## 6.5 Public QR Visitor

A person who scans a vehicle's QR code.

Typical goal:

View only the information authorized
for public access.

The visitor must not receive access
to private vehicle operations.

## 6.6 Platform Administrator

A privileged operator responsible
for platform-level administration.

This role must remain distinct
from workshop administration.

Platform administrator access must be
explicitly controlled and auditable.

Its exact operational capabilities
remain unresolved.

---

# 7. Vehicle Categories

Garage Platform should support multiple
vehicle categories through a shared
vehicle lifecycle model.

Candidate categories include:

- Motorcycles.
- Passenger cars.
- Pickup trucks.
- Commercial road vehicles.
- Other compatible vehicle categories.

The initial emphasis is on motorcycles
and passenger vehicles.

Domain rules must not assume all vehicles:

- Have a standard 17-character VIN.
- Use the same maintenance intervals.
- Use identical mileage units.
- Have the same registration information.
- Share identical service requirements.

Vehicle-specific behavior must be modeled
where the business rules actually differ.

Detailed category support requires
a dedicated product decision.

---

# 8. Proposed V1 Capabilities

The following capabilities are candidates
for the first complete commercial-oriented
product release.

Their inclusion remains subject to
scope review and acceptance.

## 8.1 Vehicle Registry

Proposed V1 capabilities:

- Vehicle registration.
- Immutable internal VehicleId.
- Vehicle identification attributes.
- Manufacturer, model, and year.
- Vehicle category.
- License plate management.
- VIN management when applicable.
- Vehicle lifecycle status.
- Historical odometer observations.
- Ownership relationships.
- Simultaneous co-owners.
- Ownership history.

Important constraints:

- VehicleId must not change
  during identification updates.
- Ownership changes must be traceable.
- Vehicle ownership does not imply
  unrestricted historical record access.
- Odometer history is authoritative
  within Vehicle Registry.

## 8.2 Maintenance History

Proposed V1 capabilities:

- Register completed maintenance.
- Record the maintenance date.
- Associate maintenance with a vehicle.
- Record the observed mileage.
- Record service details.
- Register parts used.
- Record relevant service costs.
- Identify the service origin.
- Distinguish owner-performed and
  workshop-performed maintenance.
- View a chronological service history.
- Preserve historical corrections.

The exact representation of labor,
parts, costs, attachments, and approval
status remains to be specified.

Maintenance is authoritative for
service history.

It must not directly modify
Vehicle Registry's persistence.

## 8.3 Preventive Maintenance

Proposed V1 capabilities:

- Create maintenance plans.
- Define distance-based intervals.
- Define time-based intervals.
- Track next expected service.
- Evaluate maintenance due conditions.
- Show upcoming maintenance.
- Show overdue maintenance.
- Associate plans with vehicle categories
  or individual vehicles where appropriate.

A plan may become due because of:

- Distance.
- Elapsed time.
- Both conditions, according to an
  explicitly defined business rule.

The exact scheduling algorithm
must be specified before implementation.

## 8.4 Workshop Service Management

Proposed V1 capabilities:

- Create service orders.
- Associate orders with vehicles.
- Record requested service work.
- Track order status.
- Record performed maintenance.
- Complete a service order.
- Preserve completed service history.

The following require further domain analysis:

- Approval workflows.
- Partial completion.
- Cancellation.
- Reopening.
- Multiple technicians.
- Customer-provided parts.
- Service corrections.
- Service estimates.

These decisions must not be improvised
during implementation.

## 8.5 Scheduling

Proposed V1 capabilities:

- Request a service appointment.
- View applicable availability.
- Confirm appointments.
- Reschedule appointments.
- Cancel appointments.
- Associate appointments with service work.
- Track appointment lifecycle.

The final availability model
must address resource conflicts,
duration, timezone, and authorization.

## 8.6 Spare Parts and Inventory

Proposed V1 capabilities:

- Register parts in a catalog.
- Track available stock.
- Define stock locations.
- Record stock movements.
- Receive inventory.
- Reserve stock for maintenance.
- Release reservations.
- Consume reserved stock.
- Review inventory history.
- Correct stock through authorized adjustments.

Inventory owns authoritative
stock and reservation state.

Maintenance must not directly
modify inventory records.

## 8.7 Public QR Profiles

Proposed V1 capabilities:

- Issue a vehicle-scoped QR access grant.
- Generate a public access URL.
- Render a limited public vehicle profile.
- Include approved vehicle information.
- Include approved maintenance summaries.
- Revoke an access grant.
- Rotate access credentials.
- Control public field visibility.

Public QR access must not expose
complete private vehicle records.

VIN and license plate visibility
require explicit authorization policies.

## 8.8 Notifications

Proposed V1 capabilities:

- Generate maintenance reminders.
- Notify relevant authorized recipients.
- Track delivery attempts.
- Record delivery outcomes.
- Support retry and failure handling.
- Respect notification preferences.

The first delivery channels
remain to be selected.

Notifications must not independently
calculate authoritative maintenance due dates.

## 8.9 Identity and Organization Access

Proposed V1 capabilities:

- Authenticate platform users.
- Associate users with organizations.
- Apply role or resource permissions.
- Restrict vehicle access.
- Restrict workshop operations.
- Enforce administrative boundaries.
- Record relevant security audit events.

The actual identity provider,
tenancy model, and permission strategy
remain unresolved.

V1 must not support multi-organization
data access without an approved
isolation and authorization design.

---

# 9. Proposed Release Priorities

This section proposes a planning classification.

It does not constitute an accepted delivery plan.

## Foundational V1 Capabilities

These establish the core product value:

- Authenticated vehicle management.
- Vehicle identification and ownership.
- Odometer observations.
- Maintenance history.
- Preventive maintenance.
- Basic authorization.
- Historical traceability.

## Integrated V1 Capabilities

These establish the workshop-oriented
operational experience:

- Service orders.
- Appointment scheduling.
- Inventory movements.
- Stock reservations.
- Service completion integration.
- Public QR information.
- Maintenance notifications.
- Organization administration.

## Scope Review Required

The project must decide whether the integrated
capabilities are delivered simultaneously
with foundational capabilities or released
incrementally under one approved V1 plan.

Regardless of delivery order, the architecture
must define all approved V1 integration contracts
before implementation begins.

---

# 10. Explicitly Deferred Capabilities

The following are not assumed to be
part of the initial V1 scope.

### E-Commerce

- Public online parts marketplace.
- Shopping carts.
- Consumer checkout.
- Online payment processing.

### Full ERP

- General ledger accounting.
- Payroll.
- Human resources.
- General procurement ERP.
- Enterprise financial consolidation.

### Advanced Commerce

- Automated dynamic pricing.
- Sales order processing.
- Full electronic invoicing.
- Payment reconciliation.

### Telematics

- Direct OBD integration.
- Continuous GPS vehicle tracking.
- Automatic vehicle sensor ingestion.
- Connected vehicle diagnostics.

### Advanced Intelligence

- Predictive maintenance based on ML.
- Automated damage diagnosis.
- AI-generated technical inspection.
- Computer vision for vehicle identification.

### Complex Fleet Operations

- Route optimization.
- Dispatch management.
- Driver payroll.
- Large-scale logistics optimization.

These capabilities may be evaluated
through future product specifications.

They must not be introduced automatically
into V1 domain models.

---

# 11. Representative User Journeys

## 11.1 Personal Vehicle Registration

1. A user authenticates.
2. The user requests vehicle registration.
3. The platform validates the required information.
4. Vehicle Registry creates the vehicle identity.
5. The platform establishes permitted access
   according to the authorization model.
6. The user can view the registered vehicle.

Registration must not automatically establish
unverified legal ownership.

The exact ownership verification policy
remains unresolved.

## 11.2 Owner-Performed Maintenance

1. An authorized user selects a vehicle.
2. The user records completed maintenance.
3. The user enters the relevant service information.
4. The user records an observed odometer reading.
5. Maintenance stores the completed service.
6. Maintenance publishes MaintenanceCompleted.
7. Vehicle Registry processes the odometer observation.
8. Preventive maintenance may be reevaluated.

The services are eventually consistent.

The completed maintenance must not depend
on synchronous Vehicle Registry confirmation.

## 11.3 Workshop Service

1. An authorized workshop user identifies a vehicle.
2. The workshop obtains permitted vehicle information.
3. A service order is created.
4. Required parts are identified.
5. Inventory may reserve required stock.
6. Scheduling may coordinate an appointment.
7. Authorized staff perform the service.
8. Maintenance records completion.
9. Inventory records stock consumption.
10. Relevant service and odometer events propagate.

The precise distributed workflow
must be designed separately.

This journey does not assume every operation
requires a Saga.

## 11.4 Preventive Maintenance Reminder

1. A vehicle has an active maintenance plan.
2. Relevant vehicle and maintenance facts change.
3. Maintenance evaluates the plan.
4. A due condition is identified.
5. An approved notification event is produced.
6. Notifications evaluates recipient preferences.
7. The appropriate delivery attempt occurs.
8. Delivery status is recorded.

Reminder deduplication and timing
require explicit business policies.

## 11.5 Public QR Consultation

1. An authorized participant issues a QR grant.
2. The public QR code becomes available.
3. A visitor scans the code.
4. Public Access validates the credential.
5. Public Access evaluates its permitted scope.
6. A restricted vehicle projection is returned.
7. The visitor sees only approved information.

Revocation must prevent continued access
through the revoked credential.

Public data projections may be eventually consistent,
but authorization must be enforced at access time.

## 11.6 Ownership Change

1. An authorized operation requests a change.
2. The platform validates the applicable policy.
3. Existing ownership relationships are evaluated.
4. Relevant relationships are updated atomically.
5. Historical ownership is preserved.
6. Authorized projections are reevaluated.
7. Public grants and private permissions
   are reviewed according to policy.

A transfer must not automatically
invalidate unrelated co-owners.

---

# 12. Business Data Principles

## Authoritative Ownership

Each business fact has one authoritative context.

Examples:

| Information | Context |
| --- | --- |
| Vehicle identity | Vehicle Registry |
| Ownership history | Vehicle Registry |
| Odometer history | Vehicle Registry |
| Maintenance history | Maintenance |
| Maintenance due | Maintenance |
| Inventory availability | Inventory |
| Appointments | Scheduling |
| Public access grants | Public Access |
| Notification delivery | Notifications |

A context may maintain authorized projections
of information owned elsewhere.

A projection is not automatically authoritative.

## Historical Traceability

Business records should preserve:

- Relevant identifiers.
- Effective dates.
- Recording timestamps.
- Source of information.
- Meaningful correction history.
- Applicable audit information.

Historical traceability must respect
retention and privacy requirements.

## Source Trust

Owner-reported information and
workshop-recorded information may have
different origins and verification levels.

The product must not label all historical
records as independently verified
without evidence.

The final trust and verification model
requires explicit design.

---

# 13. Product Security Principles

The product must protect:

- Personal information.
- Vehicle ownership information.
- Private maintenance records.
- Organization information.
- Inventory and operational data.
- Access credentials.
- Supporting documents.

Authorization must be enforced
by backend services.

UI visibility restrictions are insufficient
as a security mechanism.

Public access grants must be
scoped and revocable.

An organization must not automatically
receive access to another organization's
private service records.

The exact identity, tenant, and data-sharing
model remains an open V1 design decision.

---

# 14. User Experience Principles

## Clarity

The user should understand:

- Which vehicle is being viewed.
- What maintenance was performed.
- What maintenance is due.
- What actions require attention.
- Which information is public or private.

## Historical Context

Historical records should remain understandable
even after ownership or organizational changes.

## Transparency

The interface should distinguish:

- Confirmed operations.
- Pending operations.
- Failed operations.
- Information awaiting synchronization.
- Information requiring review.

## Accessibility

The user experience should support
accessible navigation and interaction.

Detailed accessibility criteria
belong in the non-functional requirements
and screen specifications.

## Portability

The experience should support future
web, mobile, and desktop applications.

Reusable application behavior must remain
independent of presentation frameworks.

---

# 15. Non-Functional Expectations

The product requires explicit decisions for:

- Availability.
- Performance.
- Data durability.
- Security.
- Privacy.
- Operational recovery.
- Auditability.
- Maintainability.
- Accessibility.
- Localization.
- Scalability.
- Deployment independence.

The non-functional requirements document
must define measurable acceptance criteria.

No numerical service-level objective
is approved in this document.

---

# 16. Product Success Criteria

The proposed product should demonstrate
the following outcomes:

### Vehicle Management

An authorized user can manage a vehicle
without losing its stable identity
after identification or ownership changes.

### Maintenance Traceability

An authorized user can understand
the maintenance history and the origin
of relevant records.

### Preventive Maintenance

The product can indicate upcoming
or overdue maintenance according to
approved distance and time policies.

### Workshop Operations

A workshop can coordinate approved
service, scheduling, and inventory workflows
without bypassing authoritative boundaries.

### Public Information Sharing

A QR visitor can access only the
authorized public projection.

Revoking the credential prevents
continued authorized access.

### Operational Reliability

Relevant workflows tolerate expected
failures, retries, and asynchronous
message delivery without duplicating
business effects.

### Authorization

The platform prevents unauthorized
cross-vehicle and cross-organization access.

### Maintainability

The architecture preserves
the approved domain and service boundaries.

Success metrics and numerical thresholds
must be established separately.

---

# 17. Major Product Risks

## Scope Expansion

Attempting to build a complete ERP,
marketplace, and workshop management suite
in the first release may dilute the core vision.

## Authorization Complexity

Vehicle ownership, workshop participation,
and organizational tenancy are related
but not interchangeable concepts.

## Historical Data Trust

Data entered by different participants
may have inconsistent verification levels.

## Integration Complexity

Distributed service workflows introduce
eventual consistency and recovery requirements.

## Domain Overgeneralization

Supporting multiple vehicle categories
must not produce unnecessary abstractions
before their differences are understood.

## Premature Technical Decisions

Technology must not determine product behavior.

Unresolved business rules must be clarified
before their implementation is designed.

---

# 18. V1 Scope Decisions Required

The following product decisions must be
resolved before final V1 scope approval.

### PS-001: Initial Customer Segment

Decide whether V1 targets:

- Individual owners first.
- Workshops first.
- Both as coordinated first-release experiences.

### PS-002: Initial Vehicle Categories

Confirm the exact vehicle categories
supported in the first release.

### PS-003: Workshop Workflow Depth

Decide whether V1 includes:

- Basic service recording.
- Complete service orders.
- Approvals and estimates.
- Technician assignments.

### PS-004: Inventory Depth

Decide whether V1 requires:

- Basic stock tracking.
- Reservations.
- Consumption.
- Multiple locations.
- Supplier operations.

### PS-005: Scheduling Depth

Define whether V1 requires
resource-based appointment availability
or a simpler appointment model.

### PS-006: Ownership Verification

Define the difference between
recorded ownership claims
and verified ownership relationships.

### PS-007: Service Record Trust

Define the visibility and verification
of self-reported versus workshop records.

### PS-008: Notifications

Choose initial channels,
recipient policies, and preferences.

### PS-009: Vehicle Attachments

Determine which photos and supporting
documents are required in V1.

### PS-010: Organizations and Tenancy

Approve the organization, membership,
and isolation model.

### PS-011: Cross-Workshop History

Define the permissions required
to access service history created
by other organizations.

### PS-012: Public QR Visibility

Define the default public fields
and permitted field-level customization.

These decisions will feed into
the use-case catalog, bounded context design,
authorization model, and ADR process.

---

# 19. Scope Approval Criteria

This document can become Accepted when:

- [ ] Target users are approved.
- [ ] The primary V1 customer segment is selected.
- [ ] Supported initial vehicle categories are defined.
- [ ] V1 capabilities are explicitly approved.
- [ ] Deferred capabilities are confirmed.
- [ ] Major business workflows have clear ownership.
- [ ] Critical authorization assumptions are resolved.
- [ ] Product success criteria are testable.
- [ ] Unresolved product decisions are assigned
      to the appropriate design documents.

Acceptance of this product scope does not
automatically approve detailed domain models,
technical contracts, or deployment decisions.

---

# 20. Related Documentation

- [Architecture Blueprint Roadmap](../architecture/blueprint-roadmap.md)
- [Architecture Constitution](../architecture/constitution.md)
- [System Overview](../architecture/system-overview.md)
- [Bounded Context Map](../architecture/diagrams/02-bounded-context-map.md)
- [Container Architecture](../architecture/diagrams/03-container-architecture.md)
- [ADR-0010: Identity and Tenancy](../architecture/decisions/0010-identity-and-tenancy.md)
- [ADR-0015: Context Boundaries](../architecture/decisions/0015-bounded-context-boundaries.md)
- [ADR-0016: Odometer Propagation](../architecture/decisions/0016-maintenance-odometer-propagation.md)

---

## Final Product Principle

Garage Platform manages the lifecycle of vehicles,
not merely a collection of vehicle records.

Identity must remain stable.

History must remain traceable.

Business ownership must remain explicit.

Access must remain controlled.

The software must evolve around real
vehicle management needs rather than
forcing those needs into technical structures.
