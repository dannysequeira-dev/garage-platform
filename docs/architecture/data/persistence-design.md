# Persistence Architecture, Schema Boundaries, and Migrations

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Database model
Each deployed service has its own logical PostgreSQL database/schema ownership, user credentials and migration history. Multiple service-owned databases may share a physical cluster. Direct cross-service joins, foreign keys and writes are forbidden.

## Candidate entity groups (not final SQL schema)
| Context | Logical tables/entities | Concurrency boundary |
| --- | --- | --- |
| Registry | vehicle, vehicle_identity_revision, vehicle_ownership, odometer_observation, odometer_state, inbox, outbox | Vehicle, ownership set, odometer state |
| Maintenance | maintenance_order, maintenance_record, plan, due_projection, outbox, inbox, saga_state | Order/plan and saga state |
| Inventory | part, stock_bucket, stock_movement, reservation, inbox, outbox | part+location stock bucket |
| Scheduling | resource_calendar, appointment, inbox, outbox | resource/time capacity |
| Public | qr_grant, qr_projection, audit | grant state / policy |
| Notifications | intent, delivery_attempt, preference projection, inbox | intent idempotency |

## Domain/persistence isolation
Domain Entities and Aggregate Roots are pure Java; JPA Entities are separate adapter models. Mappers reconstitute without emitting creation events. Repositories expose domain types via domain-service ports, not `EntityManager` or Spring Data objects.

## Transaction and outbox
Transaction port is defined in domain-service; Spring/Postgres implementation lives in dataaccess. Outbox and aggregate state changes share **one physical transaction**. A relay/CDC publishes after commit. Inbox record and associated business effects share local atomicity when required. No magic 'exactly once across services' claim.

## Concurrency and constraints
Use database uniqueness/exclusion constraints where needed (e.g. reservation key, source observation revision, organization-scoped SKU). Combine with domain transitions and optimistic/pessimistic concurrency appropriate to each invariant. Do not assume a read-then-write query prevents two concurrent reservations.

## Schema migration practice
Service owns numbered idempotent migration scripts. Consider Flyway as candidate; tool not yet accepted. Prefer expand/contract for independent deployments: add new compatible columns, deploy readers, backfill, switch writers, only then remove old columns. Avoid schema changes requiring simultaneous deploy of all services.

## Read models and history
Keep unbounded history paginated; do not hydrate every ownership relation or odometer observation with Vehicle aggregate. Index source/provenance, vehicle/time and org-scope filters. Document eventual read freshness and audit policy.

## Disaster recovery
Define logical backup+restore per service, consistency between DB restore point and Kafka retention, Outbox/Inbox replay, audit integrity and encryption. Rehearse real restore tests, not only backup success.

## Open decisions
Tenant storage (shared schema RLS/schema/db), location-specific constraints, migration tooling, event relay, retention periods, partitioning, backup intervals, indexing and encryption/key management.
