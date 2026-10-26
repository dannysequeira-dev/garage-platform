# Architecture Decision Records

This directory contains the Architecture Decision Records
(ADRs) for Garage Platform.

ADRs document why significant architectural decisions
were made, which alternatives were considered,
and what consequences are accepted.

## Decision Index

| ADR | Title | Status |
| --- | --- | --- |
| [0001](0001-monorepo-strategy.md) | Single Monorepo with Independent Build Ecosystems | Accepted |
| [0002](0002-modular-hexagonal-backend.md) | Modular Hexagonal Backend Architecture | Accepted |
| [0003](0003-rich-domain-model.md) | Rich Domain Model and Aggregate Design | Accepted |
| [0004](0004-event-driven-integration.md) | Kafka and Event-Driven Integration | Accepted |
| [0005](0005-transactional-outbox-inbox.md) | Local Transactions, Outbox, and Inbox | Accepted |
| [0006](0006-saga-coordination.md) | Saga-Based Distributed Workflow Coordination | Accepted |
| [0007](0007-framework-independent-frontend.md) | Framework-Independent Frontend Architecture | Accepted |
| [0008](0008-shared-ui-design-system.md) | Shared UI and Design System | Accepted |
| [0009](0009-contract-governance.md) | API and Integration Contract Governance | Accepted |
| [0010](0010-identity-and-tenancy.md) | Identity, Authorization, and Multi-Tenancy | Draft |
| [0011](0011-public-qr-access.md) | Scoped and Revocable Public QR Access | Accepted |
| [0012](0012-observability-strategy.md) | Observability and Infrastructure Monitoring | Draft |
| [0013](0013-delivery-and-infrastructure.md) | Delivery and Infrastructure Strategy | Draft |
| [0014](0014-testing-and-architecture-enforcement.md) | Testing and Architectural Enforcement | Accepted |
| [0015](0015-bounded-context-boundaries.md) | Candidate Bounded Contexts and Service Boundaries | Draft |

## ADR Lifecycle

### Draft

A proposed decision under discussion.

Draft ADRs do not independently authorize
unresolved implementation choices.

### Accepted

An approved architectural decision.

Implementations must comply with its applicable
requirements.

### Superseded

A historical decision replaced by a newer ADR.

Superseded records remain in version control
for traceability.

### Deprecated

A decision that should no longer guide
new implementation but has not yet been
fully replaced.

## Rules

1. ADRs must describe one coherent decision.
2. Significant alternatives must be documented.
3. Accepted decisions must not be silently rewritten.
4. Replacements should use new ADRs with references.
5. Implementation must respect accepted decisions.
6. Unresolved choices must remain explicit.
7. The Architecture Constitution has priority
   over conflicting lower-level guidance.
8. Changes to constitutional guarantees require
   a constitution amendment.

## Writing New ADRs

Each new ADR should include:

- Status.
- Date.
- Scope.
- Context.
- Decision.
- Alternatives.
- Consequences.
- Verification criteria.
- Open questions when applicable.

Number ADRs sequentially.

Do not reuse identifiers from superseded decisions.

## Related Documentation

- [Architecture Constitution](../constitution.md)
- [System Overview](../system-overview.md)
- [Repository README](../../../README.md)

## Current State

The foundational architectural decisions are documented.

Several implementation-specific decisions remain Draft
and must be resolved before their respective components
are implemented.

The project is in its architecture foundation phase.
