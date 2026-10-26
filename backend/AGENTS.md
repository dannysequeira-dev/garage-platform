# Backend Agent Instructions

**Scope:** All files under backend/

These instructions extend the root AGENTS.md.

Read the root architectural constitution and relevant ADRs
before implementing changes.

## Technology

- Java for backend implementation.
- Maven with parent POMs.
- Spring Boot for executable service containers.
- PostgreSQL for relational persistence.
- Kafka for asynchronous integration.

Do not select or upgrade framework versions without
verifying compatibility and documenting the decision.

## Dependency Rules

- Domain Core must remain framework-independent.
- Domain Service must not depend on infrastructure.
- Infrastructure adapters implement domain-defined ports.
- Container is responsible for dependency composition.
- Cross-service internal implementation dependencies are forbidden.
- Circular module dependencies are forbidden.

## Domain Modeling

Prefer rich domain behavior over unrestricted mutation.

- Do not introduce public setters for convenience.
- Protect aggregate invariants.
- Use immutable Value Objects.
- Use strongly typed identifiers.
- Preserve creation/reconstitution separation.
- Use Builders where construction complexity justifies them.
- Keep Domain Events independent of Kafka.
- Keep HTTP concerns outside domain modules.
- Avoid persistence annotations in domain models.

## Domain Service

Domain Service owns business use-case coordination.

Ports must be defined within the domain-service module.

Expected port categories:

- ports/repositories/
- ports/transactions/
- ports/listeners/
- ports/publishers/

Business rules must not be moved into DataAccess
merely to simplify implementation.

## Persistence

DataAccess implements persistence concerns.

JPA entities and domain entities are separate models.

Persistence mappings must preserve domain invariants.

Use database constraints and concurrency mechanisms
where domain correctness requires them.

Domain Service determines transaction scope.

Infrastructure implements transaction execution.

## Messaging and Consistency

Kafka consumers belong in messaging adapters.

Outbox must reliably record integration events.

Inbox or equivalent mechanisms must protect relevant
business effects against duplicate message processing.

Saga coordinates distributed workflows.

Do not replace Saga with Outbox or treat compensation
as an automatic database rollback.

## Shared Modules

Common modules must contain generic, stable abstractions.

Do not move business-specific code into common modules.

Avoid dependencies on another bounded context's domain.

## Testing

Pure Domain Core tests must run without infrastructure.

Domain Service tests should use fake or in-memory ports.

Use integration tests for persistence, Kafka, Outbox,
Inbox, and Saga behavior.

Architectural boundaries must be verified automatically
where practical.

## Implementation Protocol

Before making changes:

1. Identify the affected bounded context.
2. Read relevant ADRs.
3. Determine the affected Maven modules.
4. Identify domain invariants.
5. Check existing port contracts.

After changes:

1. Report modified modules.
2. Explain relevant design decisions.
3. Execute applicable tests when available.
4. Report unverified behavior.
5. Update documentation when required.

Do not scaffold unrelated services.

Do not commit, push, or deploy without authorization.
