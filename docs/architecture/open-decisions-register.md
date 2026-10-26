# Open Architectural Decisions Register

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
Assign an owner, alternatives, recommended starting point, and closure criteria to each implementation-blocking architectural decision. A recommendation is not acceptance.

| ID | Topic | Proposed approach to assess | Blocking dependency | Current status |
| --- | --- | --- | --- | --- |
| OD-001 | V1 product scope | Personal owner + constrained workshop workflows; phase rollout | All domain designs | Open |
| OD-002 | Party source of truth | Party directory vs Organization context | Ownership and permissions | Open |
| OD-003 | Vehicle identity/tenancy | Globally stable VehicleId with tenant-scoped grants | Vehicle/QR/security | Open; ADR-0010 |
| OD-004 | Co-ownership consistency | Vehicle-scoped locking or invariant aggregate | Ownership transactions | Open |
| OD-005 | Odometer policy | History + acceptance projection + correction epochs | Registry/Maintenance | Open |
| OD-006 | Maintenance lifecycle | Order distinct from immutable completion record | Maintenance | Open |
| OD-007 | Preventive due semantics | Due at first satisfied date or distance trigger | Maintenance | Open |
| OD-008 | Reservation consistency | StockBucket consistency boundary + durable reservation | Inventory | Open |
| OD-009 | Scheduling resources | Explicit resource calendars vs capacity slots | Scheduling | Open |
| OD-010 | Service booking workflow | Saga when stock and appointment both required | Integration | Open |
| OD-011 | QR field policy | Minimal fields; explicit VIN/plate opt-in | Privacy/Public Access | Open |
| OD-012 | Notification channels | Email first vs email + push | Notifications | Open |
| OD-013 | Kafka serialization | JSON Schema/AsyncAPI vs Avro/Protobuf registry | Event contracts | Open |
| OD-014 | Outbox relay | CDC vs polling relay | Event reliability | Open |
| OD-015 | Saga engine | Durable state machine vs workflow engine | Distributed workflow | Open |
| OD-016 | HTTP gateway/BFF | API ingress plus app composition vs BFF | Client security | Open |
| OD-017 | Identity provider | OIDC provider; evaluate Keycloak/managed | Auth | Open; ADR-0010 |
| OD-018 | Tenant persistence isolation | Shared schema RLS vs schema/db per tenant | Security/data | Open; ADR-0010 |
| OD-019 | Media storage | Private object store and signed reads | Documents | Open |
| OD-020 | Frontend libraries | State, UI primitives, realtime, generator | Frontend | Open |
| OD-021 | Hosting/orchestration | Compose/managed containers/Kubernetes | Operations | Open; ADR-0013 |
| OD-022 | Telemetry backends | OpenTelemetry + costed stores | Operations | Open; ADR-0012 |
| OD-023 | RTO/RPO and SLO | Set measurable targets from expected usage | Operations/NFR | Open |
| OD-024 | Privacy/legal | Applicable jurisdictions, consents, deletion, audit | Security | Open |
| OD-025 | Full service decomposition | Which contexts need dedicated deployments in V1 | Architecture | Open; ADR-0015 |
| OD-026 | UI languages/currencies | ES first vs EN+ES; display and locale policy | Product/UI | Open |
| OD-027 | API evolution | Version path/media-type and sunset windows | Contracts | Open |
| OD-028 | Owner verification | Assertion vs evidence-backed verification | Identity/Registry | Open |
| OD-029 | Service trust provenance | Self-reported / workshop / verified levels | Maintenance/sharing | Open |
| OD-030 | Inventory locations | Single workshop vs multiple locations | Inventory/V1 scope | Open |

## Closure workflow
1. Capture concrete scenario and requirement.
2. Compare at least two viable alternatives with business impact, complexity, risk, privacy, and cost.
3. Record the selected choice in the appropriate ADR or approved product specification.
4. Mark this register item resolved, citing the source document and version.
5. Update models, API/event contracts, diagrams, test criteria, and traceability.

## No silent defaults
Examples and diagrams in this pack are not authorization to choose a specific cloud, OIDC provider, Kafka serializer, tenancy persistence strategy, or business correction policy without review.
