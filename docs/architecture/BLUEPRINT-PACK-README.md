# Architecture Blueprint - Additive Documentation Pack

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
This pack extends the existing Garage Platform repository with proposed design documents for the entire V1 architecture review. It does **not** replace existing ADRs, constitutions, diagrams 01-07, or the existing product vision.

## Scope and authority
The root architecture constitution and **Accepted** ADRs are authoritative. Every file in this pack is a **Draft** unless a section explicitly quotes an existing accepted decision. A proposed default is **not** automatically an approved decision. Draft ADR-0010 (identity/tenancy), ADR-0012 (observability), ADR-0013 (delivery), and ADR-0015 (boundaries) still require review.

## Design-only phase
No Java/TypeScript application code, executable services, dependencies or production configuration are included. The OpenAPI and AsyncAPI files are **illustrative proposed contracts**, not production-ready APIs or approved schemas.

## Navigation
| Area | Starting document | Review focus |
| --- | --- | --- |
| Product | `docs/product/release-scope-proposal.md` | Approve V1 boundaries |
| Actors and UX | `docs/product/actors-and-personas.md` | Roles, journeys, goals |
| Requirements | `docs/product/use-case-catalog.md` | Preconditions, success, failures |
| Language | `docs/domain/glossary.md` | Ubiquitous language |
| Contexts | `docs/domain/context-catalog.md` | Ownership and integrations |
| Domain behavior | `docs/domain/*/domain-blueprint.md` | Aggregates and invariants |
| APIs and events | `docs/contracts/` | Versioning, idempotency, schemas |
| Distributed workflows | `docs/architecture/integration/workflows.md` | Saga vs events |
| Data | `docs/architecture/data/persistence-design.md` | ACID, constraints, migrations |
| Security | `docs/architecture/security/authorization-model.md` | Access grants and tenancy |
| Frontend | `docs/frontend/information-architecture.md` | Screens and reusable app layers |
| Operations | `docs/operations/deployment-topology.md` | Runtime architecture |
| Reviews | `docs/architecture/open-decisions-register.md` | Unresolved decisions |
| Traceability | `docs/architecture/traceability-matrix.md` | Requirements to design |

## How to review
1. Approve or revise `release-scope-proposal.md`; do not treat candidate V1 features as promises.
2. Review business rules and workflows with realistic scenarios, including failures.
3. Resolve open decision IDs (OD-xxx) into amendments or new ADRs.
4. Compare context models to the existing bounded context and Vehicle Registry diagrams.
5. Review API/event proposals only after business semantics are accepted.
6. Check consistency and security across contexts, including co-ownership and historical data visibility.
7. Run the final architecture readiness checklist before using Spec Kit for implementation planning.

## Existing files intentionally excluded
`README.md`, `AGENTS.md`, `CLAUDE.md`, `.specify/`, `docs/architecture/constitution.md`, `docs/architecture/decisions/0001-0016`, existing diagram files 01-07, `docs/architecture/blueprint-roadmap.md`, and `docs/product/vision-and-scope.md`.

## Delivery behavior
Unzip this pack **from the root of garage-platform**. Paths start with `docs/` and `contracts/`. Inspect the archive before extraction and use `unzip -n` to avoid overwriting existing files.
