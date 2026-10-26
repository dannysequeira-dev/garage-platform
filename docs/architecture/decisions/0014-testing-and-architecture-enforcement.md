# ADR-0014: Testing and Architectural Enforcement

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Quality gates, tests, and dependency governance
- **Related:** ADR-0002, ADR-0007

## Context

Garage Platform intentionally uses strict
architectural boundaries.

Documentation alone cannot guarantee
that future implementation respects
those boundaries.

The architecture must remain verifiable
by automated tests and static checks.

## Decision

Adopt automated architecture verification,
domain testing, integration testing,
and contract compatibility checks.

### Domain Tests

Pure domain tests must execute without:

- Spring application startup.
- Database connections.
- Kafka brokers.
- HTTP services.
- Framework-specific application containers.

Tests should validate:

- Entity identity.
- Value Object behavior.
- Aggregate invariants.
- Valid transitions.
- Invalid transitions.
- Domain Errors.
- Creation and reconstitution.
- Domain Event recording.

### Domain Service Tests

Domain Service tests should use
fake or in-memory port implementations.

These tests must verify:

- Use-case coordination.
- Repository interactions.
- Transaction boundary expectations.
- Event recording coordination.
- Failure handling.
- Business workflow transitions.

### Persistence Integration Tests

Persistence tests must verify:

- Domain/JPA mapping.
- Repository behavior.
- Database constraints.
- Concurrency control.
- Transaction propagation.
- Rollback semantics.
- Outbox atomicity.

Testcontainers may provide isolated
database dependencies.

### Kafka Integration Tests

Messaging tests should cover:

- Event serialization.
- Consumer behavior.
- Duplicate delivery.
- Retry behavior.
- Poison messages.
- Idempotent business effects.
- Relevant event ordering assumptions.

### Saga Tests

Saga verification must include:

- Successful completion.
- Duplicate responses.
- Partial failure.
- Timeout.
- Retry.
- Compensation.
- Failed compensation.
- Crash and restart recovery.
- Concurrent state updates.

### Contract Tests

API and messaging contracts must be validated.

Relevant checks include:

- Producer schema conformance.
- Consumer compatibility.
- Generated TypeScript client compilation.
- Error contract compatibility.
- Breaking-change detection.

### Frontend Tests

Frontend tests should verify:

- Framework-independent use cases.
- Adapter contracts.
- Interaction state.
- Error handling.
- Shared UI behavior.
- Accessibility.
- Rendering integration.

Core and Application tests must not require
Next.js or React to execute.

### Architecture Tests

ArchUnit is the preferred Java mechanism
for enforcing relevant dependency rules.

Mandatory checks include:

- Domain Core excludes infrastructure dependencies.
- Domain Service does not depend on adapters.
- Module dependencies remain acyclic.
- Domain models do not use JPA annotations.
- Cross-service internal imports are prohibited.

Maven dependency checks may complement
class-level architecture rules.

### TypeScript Boundaries

Frontend checks must prohibit:

- React imports in framework-independent Core.
- Next.js imports in Application.
- Infrastructure imports into domain modules.
- Circular package dependencies.
- Forbidden cross-domain implementation imports.

ESLint rules or equivalent tooling may
enforce these restrictions.

### CI Gates

A relevant change must run the checks
required by its affected modules.

Failed required checks must block integration.

Full integration verification may be
scheduled or required at release gates.

### Test Quality

Tests must validate observable behavior.

Avoid tests that merely reproduce
implementation details.

Do not create tests solely to increase
coverage metrics.

Coverage may be used as a diagnostic metric,
not as the only measure of correctness.

### AI-Assisted Changes

AI agents must report:

- Tests executed.
- Test results.
- Unverified behavior.
- Architectural changes.
- Known limitations.

Agents must not claim successful
execution of tests they did not run.

## Alternatives

### Manual Architecture Review Only

Rejected because violations may accumulate
without automated detection.

### End-to-End Tests Only

Rejected because domain rules and dependency
boundaries require more focused verification.

### Coverage Percentage as Sole Gate

Rejected because coverage does not guarantee
correct behavior or architectural integrity.

## Consequences

Positive:

- Enforceable architecture.
- Faster regression detection.
- Better refactoring safety.
- Independent domain verification.
- Increased confidence in integration changes.

Negative:

- Additional test infrastructure.
- Longer verification workflows.
- Architecture rule maintenance.
- More complex CI configuration.

## Acceptance Criteria

The architecture is considered enforced when:

1. Domain modules compile independently.
2. Forbidden dependencies fail automated checks.
3. Integration tests prove transactional guarantees.
4. Contract compatibility is verified.
5. Frontend portability is tested.
6. Required CI checks block invalid changes.

## Final Decision

Architectural rules must be backed by
automated verification wherever practical.

This decision is accepted.
