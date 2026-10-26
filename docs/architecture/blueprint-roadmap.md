# Garage Platform — Architecture Blueprint Roadmap

**Version:** 0.1.0
**Status:** In Progress
**Updated:** 2026-10-26
**Objective:** Complete the V1 architecture before implementation.

---

## 1. Mission

Garage Platform will be designed comprehensively
before backend or frontend implementation begins.

The architecture blueprint must establish:

- Business capabilities and boundaries.
- Functional and non-functional requirements.
- Domain entities, aggregates, and invariants.
- Application use cases and state transitions.
- Authoritative data ownership.
- Integration contracts and distributed workflows.
- Persistence and concurrency strategies.
- Identity, security, privacy, and authorization.
- Frontend interaction and presentation architecture.
- Deployment and operational architecture.
- Testing and architecture enforcement.
- A traceable record of architectural decisions.

Implementation must follow approved specifications,
not introduce new fundamental decisions by accident.

The goal is architectural completeness for V1,
not speculative design of every future feature.

---

## 2. Design-Only Phase

Until the architecture blueprint is approved:

- No production backend implementation.
- No production frontend implementation.
- No premature service scaffolding.
- No implementation-driven architectural shortcuts.
- No speculative infrastructure deployment.

Allowed work:

- Markdown documentation.
- ADR authoring and review.
- C4, UML, ER, state, and sequence diagrams.
- Product specifications.
- OpenAPI and event schema design.
- Security and operational design.
- Architecture validation.
- Spec Kit design documents.

Framework and dependency versions may be evaluated,
but must not be assumed without verification.

---

## 3. Existing Foundation

The repository already contains:

### Governance

- Root Architecture Constitution.
- Backend Constitution.
- Frontend Constitution.
- Spec Kit Constitution.
- Root and scoped agent instructions.

### Architecture Decisions

- ADR-0001 through ADR-0016.

Important existing Draft ADRs include:

- ADR-0010: Identity and Tenancy.
- ADR-0012: Observability.
- ADR-0013: Delivery and Infrastructure.
- ADR-0015: Bounded Context Boundaries.

These require explicit resolution before
implementing the affected V1 capabilities.

### Existing Architecture Diagrams

- 01: System Context.
- 02: Bounded Context Map.
- 03: Container Architecture.
- 04: Backend Components.
- 05: Frontend Components.
- 06: Vehicle Registry Domain Model.
- 07: Vehicle Registry Use Case Sequences.

Draft diagrams remain design proposals.

They do not independently ratify open decisions.

---

# PHASE A — PRODUCT AND BUSINESS BLUEPRINT

## 4. Product Vision and Scope

Deliverable:

    docs/product/vision-and-scope.md

Define:

- Product mission.
- Target users.
- Individual owner experience.
- Workshop experience.
- Organization administration.
- Supported vehicle categories.
- MVP and V1 scope.
- Future capabilities.
- Explicitly excluded functionality.
- Success criteria.

Every feature must belong to a defined
release scope or future backlog.

## 5. Actors and User Journeys

Deliverables:

    docs/product/actors-and-personas.md
    docs/product/user-journeys.md

Document the workflows for:

- Individual vehicle owners.
- Workshop employees.
- Workshop administrators.
- Fleet operators, if included in V1.
- Public QR visitors.
- Platform administrators.

Account identity, vehicle ownership,
and organization membership must remain distinct.

## 6. Functional Requirements

Deliverable:

    docs/product/use-case-catalog.md

Every V1 use case must define:

- Identifier.
- Business goal.
- Primary actor.
- Preconditions.
- Main success flow.
- Alternative flows.
- Failure conditions.
- Relevant business rules.
- Authorization requirements.
- Postconditions.
- Acceptance criteria.

Candidate workflows include:

- Register a vehicle.
- Correct vehicle identification.
- Establish vehicle ownership.
- Transfer vehicle ownership.
- Record an odometer observation.
- Register completed maintenance.
- Create a preventive maintenance plan.
- Calculate maintenance due.
- Schedule a service.
- Reserve inventory.
- Consume reserved parts.
- Register stock receipts and adjustments.
- Generate and revoke public QR access.
- Publish authorized public vehicle information.
- Dispatch maintenance reminders.

The catalog must resolve which workflows
belong to V1 and which are future capabilities.

## 7. Non-Functional Requirements

Deliverable:

    docs/product/non-functional-requirements.md

Define measurable requirements for:

- Availability.
- Expected load and growth.
- Latency.
- Data durability.
- Security.
- Privacy.
- Auditability.
- Accessibility.
- Localization.
- Recovery objectives.
- Maintainability.
- Deployment independence.
- Operational cost.

Do not invent performance or availability
targets without a documented rationale.

---

# PHASE B — STRATEGIC DOMAIN DESIGN

## 8. Ubiquitous Language

Deliverable:

    docs/domain/glossary.md

Define consistent business terminology.

Important terms include:

- Vehicle.
- Owner.
- Party.
- User.
- Organization.
- Workshop.
- Maintenance Order.
- Maintenance Record.
- Maintenance Plan.
- Odometer Observation.
- Current Odometer State.
- Stock Item.
- Stock Movement.
- Stock Reservation.
- Appointment.
- QR Access Grant.

Terms with different meanings across contexts
must be explicitly distinguished.

## 9. Bounded Context Boundaries

Deliverables:

    docs/domain/context-catalog.md
    docs/architecture/diagrams/02-bounded-context-map.md

Evaluate:

- Vehicle Registry.
- Maintenance.
- Inventory.
- Scheduling.
- Public Access.
- Notifications.
- Identity and Access.
- Organizations and Tenancy.
- Future Commerce.

For each context define:

- Core business responsibility.
- Authoritative data ownership.
- Published language.
- Upstream and downstream dependencies.
- Security boundaries.
- Allowed integration mechanisms.
- Candidate deployment boundary.

Complete the review of ADR-0015.

## 10. Identity and Ownership Strategy

Deliverables:

    docs/domain/identity-and-ownership-model.md
    docs/architecture/security/authorization-model.md

Resolve:

- Global versus tenant-scoped VehicleId.
- PartyId versus UserId.
- Organization membership.
- Vehicle legal ownership.
- Co-ownership.
- Historical ownership.
- Workshop permissions.
- Cross-organization service history.
- User consent and access grants.
- Tenant isolation.
- Authentication integration.

Complete or supersede the Draft decision
documented in ADR-0010 through the
established ADR governance process.

---

# PHASE C — TACTICAL DOMAIN DESIGN

## 11. Context-Level Domain Blueprints

Each V1 bounded context must define:

1. Aggregate Roots.
2. Entities.
3. Value Objects.
4. Strongly typed identifiers.
5. Domain Services.
6. Business invariants.
7. Commands and operations.
8. Domain Events.
9. Domain Errors.
10. Lifecycle state machines.
11. Transaction boundaries.
12. Concurrency requirements.
13. Persistence ownership.
14. Cross-context interactions.
15. Acceptance and failure scenarios.

Planned documentation directories:

    docs/domain/vehicle-registry/
    docs/domain/maintenance/
    docs/domain/inventory/
    docs/domain/scheduling/
    docs/domain/public-access/
    docs/domain/notifications/

Identity and Organizations receive their own
domain blueprints if their boundaries are
included in the approved V1 architecture.

## 12. Vehicle Registry Design

Resolve:

- Vehicle identity and lifecycle.
- VIN and license plate policies.
- Party identity reference.
- Ownership Aggregate Root boundaries.
- Co-ownership and transfer constraints.
- Ownership temporal model.
- Odometer observation storage.
- Current odometer acceptance rules.
- Corrections and replacements.
- Historical event idempotency.
- Authorization implications.

Existing draft diagrams provide the starting point.

## 13. Maintenance Design

Resolve:

- Maintenance Order lifecycle.
- Completed Maintenance Record lifecycle.
- Preventive Maintenance Plan.
- Due calculations by date and distance.
- Maintenance service categories.
- Labor and parts references.
- Workshop versus owner-performed service.
- Approval, cancellation, and correction.
- Historical maintenance entry.
- Maintenance completion events.
- Required inventory and scheduling interactions.

## 14. Inventory Design

Resolve:

- Part identity and catalog.
- SKU and compatible vehicle models.
- Stock locations.
- Inventory movements.
- Receipts and adjustments.
- Reservations and expiration.
- Reservation consumption and release.
- Concurrency and overselling prevention.
- Stock reconciliation.
- External supplier information.

## 15. Scheduling Design

Resolve:

- Availability and capacity.
- Appointment lifecycle.
- Service duration.
- Staff or resource assignment if in scope.
- Reservation conflicts.
- Confirmation and cancellation.
- Rescheduling.
- Timezones.
- Maintenance booking integration.

## 16. Public Access Design

Resolve:

- Public QR grant lifecycle.
- Token issuance and verification.
- Revocation and rotation.
- Expiration.
- Field-level public projections.
- Data refresh and invalidation.
- Ownership change implications.
- Rate limiting and privacy.

## 17. Notifications Design

Resolve:

- Notification triggers.
- Recipient selection.
- Channel preferences.
- Message templates.
- Deduplication.
- Delivery retries.
- Failure handling.
- Reminder scheduling.
- Delivery status.
- Opt-out and consent requirements.

---

# PHASE D — INTEGRATION BLUEPRINT

## 18. Event Catalog

Deliverable:

    docs/contracts/events/event-catalog.md

Define each Integration Event with:

- Owning producer.
- Business meaning.
- Version.
- Aggregate identifier.
- Payload.
- Correlation metadata.
- Ordering assumptions.
- Duplicate handling.
- Consumer expectations.
- Data classification.
- Compatibility policy.

Include MaintenanceCompleted and
VehicleOdometerRecorded where applicable.

## 19. Command and API Catalog

Deliverables:

    docs/contracts/http/api-catalog.md
    contracts/openapi/

For each relevant API define:

- Resource and operation.
- Request and response models.
- Authorization scope.
- Error contract.
- Idempotency.
- Pagination and filtering.
- Versioning.
- Consistency expectations.

Do not expose Aggregate Roots directly
as HTTP transport representations.

## 20. Distributed Workflow Catalog

Deliverable:

    docs/architecture/integration/workflows.md

Classify each cross-context workflow as:

- Synchronous request.
- Asynchronous event reaction.
- Saga.
- Scheduled or batch operation.

For each workflow document:

- Success path.
- Failure path.
- Timeout.
- Retry.
- Deduplication.
- Compensation when applicable.
- Recovery.
- Observable operation status.

Outbox, Inbox, and Saga must retain
separate responsibilities.

## 21. Messaging Decisions

Resolve:

- Kafka topic ownership and naming.
- Partition keys.
- Event serializer and schema registry.
- Contract evolution.
- Outbox relay implementation.
- Inbox persistence and retention.
- Dead-letter strategy.
- Consumer retries and backpressure.
- Replay and recovery.
- Event ordering guarantees.

---

# PHASE E — DATA, SECURITY AND PRIVACY

## 22. Persistence Architecture

Deliverables:

    docs/architecture/data/data-ownership.md
    docs/architecture/data/persistence-design.md

For each service define:

- Logical database ownership.
- ER diagram.
- Required constraints.
- Unique indexes.
- Transaction boundaries.
- Concurrency mechanisms.
- Migration ownership.
- Retention policy.
- Deletion and archival requirements.
- Read projections.
- Backup and recovery needs.

Do not use cross-service database foreign keys.

## 23. Security and Identity

Deliverables:

    docs/architecture/security/threat-model.md
    docs/architecture/security/authorization-matrix.md

Resolve:

- Identity provider.
- Authentication flows.
- Authorization model.
- Resource ownership and grants.
- Tenant isolation.
- Sensitive data classification.
- Public QR threat scenarios.
- Secrets management.
- Audit events.
- Encryption requirements.
- Security monitoring.

## 24. Media and Document Storage

Deliverable:

    docs/architecture/data/media-and-documents.md

Define:

- Vehicle images.
- Maintenance attachments.
- Ownership supporting documents.
- Storage ownership.
- Access control.
- Signed access URLs.
- File validation.
- Upload limits.
- Malware handling.
- Retention and deletion.
- Backup implications.

Storage technology remains an explicit decision.

---

# PHASE F — FRONTEND AND EXPERIENCE DESIGN

## 25. Information Architecture

Deliverable:

    docs/frontend/information-architecture.md

Define:

- Navigation.
- Route inventory.
- User roles and accessible sections.
- Vehicle dashboard.
- Maintenance history views.
- Inventory screens.
- Scheduling screens.
- Public QR view.
- Organization administration.

## 26. UI and Interaction Flows

Deliverables:

    docs/frontend/user-flows.md
    docs/frontend/screen-catalog.md

For each screen define:

- Purpose.
- Actor.
- Required data.
- Actions.
- Empty states.
- Loading states.
- Error states.
- Validation.
- Permissions.
- Accessibility.
- Responsive behavior.

## 27. Frontend Technical Decisions

Resolve:

- Workspace package boundaries.
- HTTP client generation.
- Server-state strategy.
- Interaction-state strategy.
- Realtime integration.
- Authentication integration.
- SSR and caching boundaries.
- Design tokens.
- Shared UI component technology.
- Mobile and desktop portability.

Maintain framework independence
for Core and Application.

---

# PHASE G — OPERATIONS AND DELIVERY

## 28. Infrastructure Architecture

Deliverables:

    docs/operations/deployment-topology.md
    docs/operations/environment-strategy.md

Resolve:

- Hosting and runtime model.
- Service ingress.
- Database infrastructure.
- Kafka hosting.
- Container orchestration.
- Networking and TLS.
- Environment isolation.
- Runtime configuration.
- Secrets.
- Storage.
- Scaling.
- Availability.

Complete ADR-0013 before V1 deployment design
is considered approved.

## 29. Observability

Deliverable:

    docs/operations/observability.md

Resolve:

- OpenTelemetry collection.
- Metrics storage.
- Dashboards.
- Logging and search.
- Distributed tracing.
- Infrastructure monitoring.
- Alerting.
- Retention and costs.
- Business workflow monitoring.

Complete ADR-0012.

## 30. Delivery and Recovery

Deliverables:

    docs/operations/ci-cd.md
    docs/operations/disaster-recovery.md

Define:

- Independent builds.
- Affected-module checks.
- Architecture enforcement.
- Contract validation.
- Container images.
- Artifact versioning.
- Deployment promotion.
- Database migration sequencing.
- Rollback strategy.
- Backup and restore.
- Recovery objectives.
- Secret rotation.
- Operational ownership.

---

# PHASE H — DESIGN REVIEW AND FREEZE

## 31. Traceability

Deliverable:

    docs/architecture/traceability-matrix.md

Every V1 requirement should be traceable to:

- Owning bounded context.
- Relevant domain model.
- Required use case.
- Integration contracts.
- Persistence implications.
- Authorization rule.
- Acceptance tests.
- Relevant ADRs.

## 32. Architecture Decision Closure

All implementation-blocking decisions must be:

- Accepted.
- Explicitly deferred outside V1.
- Or resolved through an approved amendment.

Draft ADRs must not be silently considered accepted.

New decisions receive new sequential ADR numbers.

Historical ADRs remain available for traceability.

Accepted decisions must not be casually rewritten
to conceal an architectural change.

## 33. Architecture Review Checklist

V1 is ready for implementation only when:

- [ ] Product scope and exclusions are approved.
- [ ] V1 use cases have acceptance criteria.
- [ ] Business terms are consistent.
- [ ] V1 bounded contexts have clear ownership.
- [ ] Aggregate boundaries are validated.
- [ ] Domain state machines are documented.
- [ ] Cross-aggregate consistency is addressed.
- [ ] Cross-context workflows are specified.
- [ ] Integration contracts are versioned.
- [ ] API authorization is defined.
- [ ] Data isolation is defined.
- [ ] Persistence and concurrency are specified.
- [ ] Public data and privacy policies are defined.
- [ ] Frontend routes and interactions are designed.
- [ ] Deployment and recovery strategies are approved.
- [ ] Observability responsibilities are documented.
- [ ] Architecture verification gates are defined.
- [ ] No V1-blocking architectural decisions remain open.

## 34. Implementation Handoff

When the checklist is approved:

1. Freeze the V1 Architecture Blueprint baseline.
2. Record the approved architectural version.
3. Use Spec Kit to define implementation features.
4. Generate technical plans against accepted decisions.
5. Derive tasks and tests from approved specifications.
6. Implement bounded, independently verifiable slices.

Any implementation discovery requiring a fundamental
architecture change must return to the ADR process.

The implementation must not invent missing
business rules or cross-context contracts.

---

## Final Principle

Business requirements define domain behavior.

Domain invariants determine aggregate boundaries.

Bounded contexts determine authoritative ownership.

Integration contracts connect independent contexts.

Infrastructure serves the business architecture.

Implementation begins only after the V1 design
has passed its architecture readiness review.
