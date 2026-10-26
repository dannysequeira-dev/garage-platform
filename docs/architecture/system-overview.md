# System Overview

**Project:** Garage Platform
**Version:** 0.1.0
**Status:** Draft
**Scope:** System architecture and bounded-context design

---

## 1. Purpose

This document describes the high-level system architecture,
business capabilities, candidate bounded contexts, service
boundaries, data ownership, and integration responsibilities.

It complements the Architecture Constitution.

This document distinguishes between:

- Accepted architectural principles.
- Proposed service boundaries.
- Future capabilities.
- Unresolved architectural decisions.

Proposed boundaries must not be interpreted as final
implementation decisions until explicitly approved.

## 2. Product Vision

Garage Platform manages the lifecycle of vehicles,
independently of any particular workshop or owner.

Vehicles maintain stable system identities while ownership,
registration details, maintenance history, and relationships
evolve over time.

The platform supports:

- Individual vehicle owners.
- Organizations and workshops.
- Vehicle maintenance management.
- Preventive maintenance planning.
- Appointment scheduling.
- Spare parts and inventory operations.
- Controlled public vehicle information.
- Future commercial operations.

The long-term objective is a commercially viable,
multi-organization platform.

## 3. Fundamental Business Concepts

### 3.1 Vehicle Identity

A vehicle has an internal identity that remains stable
throughout its lifecycle.

Its internal identifier must not depend on:

- License plate.
- Owner identity.
- Organization identity.
- Registration status.
- External system identifiers.

A license plate may change.

A VIN is an important vehicle identifier but must not
replace the internal identity.

Correction and verification of vehicle identification
data require explicit domain behavior.

### 3.2 Ownership

Vehicles and owners have a many-to-many relationship
across time.

A vehicle may have multiple owners simultaneously,
subject to applicable ownership rules.

A person or legal entity may own multiple vehicles.

Ownership relationships must support historical
traceability and temporal validity.

Ownership history must not be destroyed when
a vehicle changes owners.

Ownership does not automatically grant access
to every historical record maintained by every
organization or workshop.

### 3.3 Maintenance

Maintenance operations represent work performed
on a vehicle.

A maintenance record may include:

- Service type.
- Completion date.
- Recorded odometer reading.
- Description.
- Performed operations.
- Parts consumed.
- Service provider.
- Monetary amounts.
- Supporting documents.

Maintenance plans define when future service
is recommended or required.

A maintenance due condition may be based on:

- Date.
- Odometer reading.
- Both date and odometer, whichever occurs first.
- Other domain-specific conditions introduced later.

Odometer-based predictions require reliable readings.

Predictions must not be represented as actual readings.

### 3.4 Inventory

Spare parts and stock are distinct concepts.

A spare part describes an identifiable product or item.

Stock represents availability at a specific inventory
location or within an authorized inventory scope.

Inventory changes must be traceable.

Reservation and consumption are separate operations.

Stock consumption must be associated with a legitimate
business operation when applicable.

### 3.5 Public Vehicle Access

A public QR code provides restricted access to
an explicitly authorized vehicle information projection.

It must not provide unrestricted access to
the underlying vehicle aggregate.

Public access must support revocation and
credential rotation.

### 3.6 Organizations and Access

A vehicle owner, a platform user, and an organization
are different concepts.

An individual may participate in multiple organizations.

An organization may be associated with multiple vehicles.

Access to vehicle information must be based on
explicit authorization rather than implicit ownership
of a database record.

---

# Part I - System Architecture

## 4. Architectural Style

The backend follows:

- Domain-Driven Design.
- Hexagonal Architecture.
- Event-Driven Architecture.
- Independent microservice deployment.
- Database ownership per service.
- Local ACID transactions.
- Transactional Outbox and Inbox.
- Saga coordination for distributed workflows.

The frontend follows:

- Domain-oriented packages.
- Framework-independent application logic.
- Ports and adapters.
- Shared UI and design systems.
- Independent application composition roots.

Backend business rules are authoritative.

## 5. Logical System Topology

The platform contains four primary architectural areas.

### 5.1 Client Applications

Client applications provide user interaction and
presentation.

Initial application:

- Web application using Next.js.

Potential future applications:

- Mobile application.
- Desktop application.

All applications should reuse compatible
TypeScript domain and application packages.

### 5.2 API Entry Layer

The API entry layer is responsible for:

- Request routing.
- Authentication integration.
- Transport-level security controls.
- Rate limiting where required.
- Request correlation.

Business authorization remains the responsibility
of the relevant backend service.

The final gateway implementation remains an
architectural decision.

### 5.3 Backend Services

Backend services are implemented in Java.

Each service:

- Owns a defined business capability.
- Has an independent deployment lifecycle.
- Owns its persistence model.
- Defines explicit integration contracts.
- Contains its own domain boundaries.
- Implements its own transactions.
- Exposes only approved external interfaces.

### 5.4 Asynchronous Integration

Apache Kafka provides asynchronous communication
between services.

Integration events communicate completed business facts.

Kafka must not become a substitute for all
synchronous communication.

Services may use synchronous APIs when an operation
requires an immediate answer from another context.

---

# Part II - Candidate Bounded Contexts

## 6. Vehicle Registry

**Proposed service:** vehicle-service

### Responsibilities

- Vehicle registration.
- Vehicle identity.
- Vehicle identification data.
- Vehicle classification.
- Ownership relationships.
- Historical ownership traceability.
- Odometer readings.
- Vehicle lifecycle status.

### Candidate Aggregates

- Vehicle.
- Party.
- VehicleOwnership or OwnershipRecord.

The precise aggregate boundaries remain open.

Ownership history must not automatically be modeled
as an unrestricted mutable collection inside Vehicle.

Concurrency, temporal rules, and ownership invariants
will determine the final aggregate boundaries.

### Example Business Operations

- RegisterVehicle.
- UpdateVehicleIdentification.
- RecordOdometerReading.
- AssignVehicleOwner.
- TransferVehicleOwnership.
- CloseOwnershipPeriod.

### Candidate Integration Events

- VehicleRegistered.
- VehicleIdentificationUpdated.
- VehicleOdometerRecorded.
- VehicleOwnershipAssigned.
- VehicleOwnershipTransferred.

These are conceptual event names, not approved
Kafka topic names or final schemas.

### Data Ownership

Vehicle Registry owns authoritative vehicle identity
and ownership relationship data.

Other services reference vehicles through identifiers
and maintain local projections when needed.

## 7. Maintenance

**Proposed service:** maintenance-service

### Responsibilities

- Maintenance records.
- Completed service history.
- Preventive maintenance plans.
- Maintenance schedules and due conditions.
- Service orders.
- Maintenance workflow coordination.
- Service completion.

### Candidate Aggregates

- MaintenanceOrder.
- MaintenanceRecord.
- MaintenancePlan.

The final distinction between service orders and
completed maintenance records remains open.

### Example Business Operations

- CreateMaintenanceOrder.
- RegisterMaintenanceRecord.
- ConfigureMaintenancePlan.
- EvaluateMaintenanceDue.
- CompleteMaintenanceOrder.
- CancelMaintenanceOrder.

### Candidate Integration Events

- MaintenanceOrderCreated.
- MaintenanceCompleted.
- MaintenanceDueDetected.
- MaintenancePlanUpdated.

### Data Ownership

Maintenance owns maintenance history, service plans,
and authoritative maintenance due calculations.

Other services must not independently decide
authoritative maintenance due status.

Maintenance may consume vehicle identity and
odometer events from Vehicle Registry.

A maintenance completion may include a new
odometer reading.

The command and event flow for updating the
authoritative odometer history must be explicitly
defined to avoid competing sources of truth.

## 8. Inventory

**Proposed service:** inventory-service

### Responsibilities

- Spare parts catalog.
- Inventory locations.
- Stock quantities.
- Stock movements.
- Stock reservations.
- Stock consumption.
- Inventory adjustments.
- Stock availability.

### Candidate Aggregates

- Part.
- StockItem.
- InventoryReservation.

Aggregate design must account for concurrent
stock reservations and adjustments.

### Example Business Operations

- RegisterPart.
- ReceiveStock.
- ReserveStock.
- ReleaseReservation.
- ConsumeReservedStock.
- AdjustStock.

### Candidate Integration Events

- StockReceived.
- StockReserved.
- StockReservationRejected.
- StockReservationReleased.
- StockConsumed.
- StockAdjusted.

### Data Ownership

Inventory owns stock availability and inventory
movement records.

Maintenance must not directly modify inventory tables.

Reservation and consumption must be explicit
business operations.

A future sales or commerce context may request
stock reservation and consumption without taking
ownership of Inventory's stock model.

## 9. Scheduling

**Proposed service:** scheduling-service

### Responsibilities

- Appointment requests.
- Appointment availability.
- Booking confirmation.
- Appointment cancellation.
- Appointment rescheduling.
- Resource availability when applicable.

### Candidate Aggregates

- Appointment.
- AvailabilitySchedule.

### Example Business Operations

- RequestAppointment.
- CheckAvailability.
- ConfirmAppointment.
- RescheduleAppointment.
- CancelAppointment.

### Candidate Integration Events

- AppointmentRequested.
- AppointmentConfirmed.
- AppointmentRescheduled.
- AppointmentCancelled.

### Data Ownership

Scheduling owns appointments and availability.

Maintenance may reference appointments but must
not become the authoritative scheduling database.

Appointment availability and reservation rules
remain inside Scheduling.

## 10. Public Access

**Proposed service:** public-access-service

### Responsibilities

- Public vehicle access grants.
- QR token issuance.
- Credential validation.
- Credential revocation.
- Credential rotation.
- Public vehicle information projections.

### Candidate Aggregates

- VehicleAccessGrant.

### Example Business Operations

- IssueVehicleAccess.
- RevokeVehicleAccess.
- RotateVehicleAccess.
- ResolvePublicVehicleProfile.

### Data Ownership

Public Access owns access grants and public
projection data.

It does not own authoritative vehicle identity
or maintenance history.

Public projections must contain only approved fields.

Private owner information must not be included
without explicit authorization.

Access tokens must be unpredictable.

Only secure token verification material should
be persisted.

Public access policies must be reevaluated when
relevant ownership or authorization changes occur.

## 11. Notifications

**Proposed service:** notification-service

### Responsibilities

- Notification delivery.
- Notification templates.
- Delivery attempts.
- Delivery status.
- Notification preferences where appropriate.
- Reminder execution.

### Candidate Operations

- ScheduleNotification.
- DeliverNotification.
- CancelPendingNotification.
- RecordDeliveryFailure.

### Candidate Integration Events

- NotificationScheduled.
- NotificationDelivered.
- NotificationFailed.

### Data Ownership

Notifications owns delivery state.

It must not independently calculate authoritative
maintenance due conditions.

It consumes relevant events and schedules delivery
according to the applicable notification policy.

An initial implementation may be a Kafka consumer
without a public HTTP API.

---

# Part III - Supporting Contexts

## 12. Identity and Access

Identity and authorization are mandatory capabilities.

Potential responsibilities include:

- User identity.
- Authentication integration.
- Organization membership.
- Role assignments.
- Vehicle access permissions.
- Workshop access permissions.

The final architecture has not determined whether
organization and authorization management will be
implemented as a dedicated service, a supporting
context, or a combination with an external identity
provider.

An external identity provider may authenticate users.

Authentication does not replace domain authorization.

A vehicle owner must not be assumed to be the
same entity as a platform user.

## 13. Organizations and Multi-Tenancy

The platform is intended to support multiple
organizations in the future.

Organization isolation must be considered early.

The exact persistence strategy remains open.

Possible models include:

- Tenant-scoped records within service databases.
- Dedicated schemas.
- Dedicated databases.
- Hybrid isolation models.

A globally shared vehicle registry versus
tenant-local vehicle identities remains an
important architectural decision.

Cross-organization vehicle history must never
become implicitly visible merely because two
records reference the same VIN.

## 14. Future Commerce Context

The platform may eventually support:

- Spare parts sales.
- Product pricing.
- Customer orders.
- Invoicing.
- Payment integrations.
- Returns and refunds.

These capabilities are not automatically owned
by Inventory.

Inventory remains responsible for stock integrity.

A future Commerce context may own orders,
pricing rules, and financial transaction workflows.

The initial implementation does not require
a complete commerce service.

---

# Part IV - Context Relationships

## 15. Context Ownership Matrix

| Business Information | Authoritative Context |
| --- | --- |
| Vehicle identity | Vehicle Registry |
| Vehicle identification attributes | Vehicle Registry |
| Ownership relationships | Vehicle Registry |
| Odometer history | Vehicle Registry |
| Maintenance records | Maintenance |
| Preventive maintenance plans | Maintenance |
| Maintenance due status | Maintenance |
| Spare parts stock | Inventory |
| Inventory movements | Inventory |
| Stock reservations | Inventory |
| Appointment availability | Scheduling |
| Appointment status | Scheduling |
| Public QR credentials | Public Access |
| Public vehicle projections | Public Access |
| Notification delivery | Notifications |
| User authentication | Identity provider / TBD |
| Organization authorization | Access context / TBD |
| Sales orders | Future Commerce |

No service may directly mutate another context's
authoritative data.

Cross-context references use identifiers and
approved contracts.

## 16. Context Communication

Context integration may use:

- Synchronous API requests.
- Kafka integration events.
- Locally maintained read projections.
- Saga coordination.

Each integration must define:

- Source context.
- Destination context.
- Business purpose.
- Contract version.
- Consistency expectations.
- Failure behavior.
- Authorization requirements.

Cross-service database joins are prohibited.

Read models may contain denormalized information
received through approved integration mechanisms.

## 17. Synchronous Communication

Synchronous communication is appropriate when
the requesting operation requires an immediate
response from another context.

Examples may include:

- Checking available appointment slots.
- Retrieving current authorized vehicle details.
- Requesting a new inventory reservation.

Synchronous calls must define failure and timeout
behavior.

Long synchronous dependency chains should be avoided.

## 18. Asynchronous Communication

Kafka events allow services to react to completed
business facts.

Examples include:

- Vehicle registration.
- Maintenance completion.
- Ownership transfer.
- Appointment confirmation.
- Stock movement confirmation.

Event consumers must tolerate duplicate delivery.

Event ordering guarantees must be designed per
business key and processing requirements.

Integration payloads must avoid exposing unrelated
personal or sensitive information.

---

# Part V - Distributed Consistency

## 19. Transactional Outbox

Each service may use a local outbox when reliable
integration event publication is required.

Business state and outbox records must be persisted
within one local transaction.

Outbox messages are published after transaction commit.

Consumers must assume that duplicate messages
are possible.

## 20. Transactional Inbox

Consumers requiring durable deduplication should
record processed message identifiers.

Inbox state and associated business effects should
be persisted atomically when applicable.

Idempotency applies to the entire business effect,
not merely message receipt.

## 21. Saga Coordination

Saga is used for business operations spanning
multiple independently owned transactional boundaries.

Outbox does not replace Saga.

Saga does not replace Outbox.

The service that owns a business workflow should
normally own its saga coordination.

### Example: Maintenance Booking

A proposed maintenance booking workflow:

1. Maintenance creates a pending maintenance order.
2. The owning saga requests an Inventory reservation.
3. Inventory confirms or rejects the reservation.
4. The saga requests an appointment from Scheduling.
5. Scheduling confirms or rejects availability.
6. Maintenance confirms the business workflow.
7. If a later step fails, the saga requests valid
   compensating actions where applicable.

Releasing a reserved part is an example of a
compensation.

Physical work already performed must not be
treated as automatically reversible.

The final process and saga ownership require a
dedicated workflow design.

## 22. Consistency Expectations

Not all system views update simultaneously.

Authoritative operations require their owning
service to validate applicable invariants.

Read projections may be eventually consistent.

The user interface must distinguish pending,
confirmed, and failed distributed operations.

Commands involving multiple services must not
be reported as fully completed until the relevant
business completion criteria are satisfied.

---

# Part VI - Frontend Architecture

## 23. Frontend Applications

The frontend is a TypeScript workspace.

The initial application will use Next.js.

Future applications may use mobile or desktop
technologies without rebuilding the same
application behavior from scratch.

Frontend applications are composition roots.

They configure:

- Domain application modules.
- HTTP adapters.
- Realtime adapters where required.
- Authentication integrations.
- Presentation adapters.
- Framework-specific infrastructure.

## 24. Frontend Domain Packages

Candidate frontend domains:

- vehicles
- maintenance
- inventory
- scheduling
- public-access

A typical domain may contain:

    core/
    application/
    adapters/
    presentation/

The reusable core and application modules must
not depend on UI frameworks.

Frontend models should primarily represent
application state, interaction behavior,
and appropriate data projections.

They must not independently reproduce all
authoritative backend aggregates and rules.

## 25. Shared User Interface

The frontend supports a reusable design system.

Candidate shared packages include:

- design-tokens
- ui-web
- ui-native
- shared interaction utilities

Web and desktop may share React-based UI components
when compatible.

Native mobile components may use separate
implementations built from shared design tokens.

Domain-specific UI components should remain
inside their domain presentation packages
unless broader reuse is justified.

## 26. API Integration

Backend APIs expose versioned contracts.

TypeScript clients may be generated from OpenAPI.

Generated transport models should not automatically
become internal application models.

HTTP adapters translate external data into
appropriate frontend representations.

Applications must not connect directly to Kafka.

Realtime updates may be exposed through approved
HTTP-based interfaces, SSE, or WebSocket.

---

# Part VII - Security and Privacy

## 27. Access Control

Authentication and authorization are separate concerns.

Every protected backend operation must validate
the relevant access permissions.

Tenant and organization boundaries must be enforced
by the backend.

Frontend visibility checks do not replace
backend authorization.

## 28. Vehicle History Privacy

Vehicle history includes potentially sensitive data.

The platform must distinguish between:

- Vehicle technical information.
- Ownership and identity information.
- Organization-owned service records.
- Private maintenance documents.
- Publicly shareable vehicle information.

A vehicle transfer does not automatically transfer
permission to access every historical document.

Cross-organization history sharing requires an
explicit policy and authorization model.

## 29. Public QR Information

A public QR profile may expose selected information:

- Public vehicle identification.
- Vehicle make and model.
- Vehicle type.
- Authorized registration attributes.
- Last approved maintenance summary.
- Next maintenance due summary.

A full VIN must not be exposed automatically.

Public profiles must not reveal:

- Private owner contact information.
- Private documents.
- Internal authorization data.
- Unrelated vehicles.
- Unapproved workshop records.

Public access credentials must be revocable.

---

# Part VIII - Observability

## 30. Telemetry

Services should emit standardized telemetry.

The proposed observability architecture includes:

- OpenTelemetry for instrumentation.
- Prometheus for metrics.
- Grafana for operational dashboards.
- Elasticsearch and Kibana for centralized logs.
- Zabbix for infrastructure monitoring.

Actual deployment of each component may be
phased according to operational requirements.

## 31. Distributed Correlation

Distributed operations should carry:

- Correlation identifiers.
- Event identifiers.
- Causation identifiers.
- Saga identifiers where applicable.
- Trace context.

Identifiers must be propagated across
appropriate synchronous and asynchronous boundaries.

Sensitive data must not be included in telemetry
without a justified and secure policy.

---

# Part IX - Delivery and Evolution

## 32. Repository Structure

The platform uses a single Git repository.

    garage-platform/
        backend/
        frontend/
        contracts/
        infra/
        docs/

The repository hosts independently deployable
services and applications.

Backend uses Maven parent POMs.

Frontend uses pnpm workspaces.

Shared documentation and contracts are versioned
with the implementation.

## 33. Initial Vertical Slice

The first proposed vertical slice focuses on
Vehicle Registry.

Candidate steps:

1. Define the initial Vehicle aggregate.
2. Define strong identifiers and value objects.
3. Implement the domain-service use case.
4. Implement the persistence adapter.
5. Expose a versioned HTTP API.
6. Generate a TypeScript API client.
7. Implement a framework-independent frontend
   application module.
8. Implement an HTTP adapter.
9. Render vehicle information through Next.js.
10. Add relevant tests and architecture checks.

Kafka integration should be introduced through
a concrete cross-context workflow rather than
as isolated infrastructure without behavior.

The first complete event-driven slice may extend
Vehicle Registry into Public Access or another
approved consumer context.

## 34. Future Capabilities

Possible future capabilities include:

- Advanced maintenance forecasting.
- Digital service records.
- Workshop operations.
- Commercial parts sales.
- Payment processing.
- Invoicing.
- Vehicle history sharing.
- Mobile applications.
- Desktop applications.
- External integrations.

These capabilities are not part of the initial
approved implementation scope.

## 35. Open Decisions

The following decisions require further design:

1. Final service boundaries.
2. Vehicle ownership aggregate design.
3. Global versus tenant-scoped vehicle identities.
4. Organization and access management boundaries.
5. Maintenance order lifecycle.
6. Exact Saga orchestration approach.
7. Event schema format and versioning.
8. Data sharing across organizations.
9. Multi-tenancy persistence strategy.
10. API Gateway implementation.
11. Realtime communication strategy.
12. Frontend shared UI technology.
13. Initial deployment topology.
14. Commercial product model and licensing.

These decisions must be resolved through
design documentation and ADRs.

## 36. Architecture Evolution

Bounded contexts may evolve as domain knowledge grows.

A context must not be split merely to increase
the number of microservices.

Architectural changes should consider:

- Business ownership.
- Consistency boundaries.
- Coupling.
- Deployment independence.
- Operational cost.
- Security implications.
- Contract compatibility.

The system should preserve clear domain ownership
while allowing controlled architectural evolution.

---

## 37. Final Principle

A vehicle exists independently of a particular
workshop, application, or owner.

Each bounded context owns its own business truths.

Microservices coordinate through explicit contracts,
not shared persistence.

Frontend applications adapt shared TypeScript
behavior to different platforms.

The monorepo provides shared engineering context
without sacrificing independent deployment.
