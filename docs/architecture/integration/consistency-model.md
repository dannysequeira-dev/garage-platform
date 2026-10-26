# Consistency, Projections, and Temporal Semantics

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Consistency boundaries
**Strong/local:** entity/aggregate invariant and owning service's database transaction; may include its Inbox and Outbox writes. **Eventual/cross-context:** service-owned replicas/projections updated by messages; no global ACID transaction or direct cross-service table join.

## Domain time vs processing time
- `effectiveAt` determines when an ownership relation applies.
- `observedAt` determines when a mileage reading was observed.
- `completedAt` denotes performed/completed maintenance fact.
- `recordedAt` denotes acceptance into system history.
- `occurredAt` belongs to the Integration Event envelope.
These timestamps may differ; late entry and replay are normal business cases.

## Projection contract
A projection should carry source ID, source revision/sequence where applicable, processed timestamp and freshness/lag indicator. Consumers may use ACL mapping; no consumer may alter authoritative source values. Access policies apply even if the projection is stale.

## Read behavior
A user who just completed maintenance may see a completion confirmation while the odometer summary is still synchronizing. UI must not imply both services committed together. Service history can render from Maintenance immediately; current mileage is read from Registry and marked pending/stale if needed.

## Reconciliation
Periodic reconciliation is necessary for missed/unprocessable events and eventual consistency stuck states. It must be idempotent and preserve provenance. Define source cross-check APIs/events without coupling DBs.

## Aggregate loading
Do not load all historical ownerships or odometer observations as mutable children on every vehicle command. Maintain transaction-safe consistency boundaries and query/projection models distinct from aggregate behavior.

## Ordering
Kafka may preserve order per partition under conditions, but across sources, replay and corrections break simple arrival-order assumptions. Business state must use causal/source revision and effective-time semantics rather than the latest consumer delivery timestamp.

## Verification
Test both eventual success and permanent inconsistent states with recovery; include old maintenance arriving late, owner transfer during QR read, duplicate reservation result and delayed Saga compensation.
