# ADR-0009: API and Integration Contract Governance

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Service APIs, event schemas, and generated clients
- **Related:** ADR-0004, ADR-0007

## Context

Garage Platform contains multiple Java services
and TypeScript client applications.

Independent deployments require explicit
and compatible integration contracts.

Sharing a repository must not justify
implicit implementation coupling.

## Decision

Use explicit, versioned contracts for
synchronous and asynchronous integration.

### HTTP APIs

OpenAPI is the preferred contract description
for synchronous service APIs.

HTTP contracts should define:

- Operations.
- Request schemas.
- Response schemas.
- Authentication requirements.
- Error representations.
- Relevant compatibility guarantees.

### Async Integration

Kafka Integration Events must have explicitly
documented and versioned schemas.

AsyncAPI may document messaging interfaces.

The exact serialization format and registry
technology remain open decisions.

### Contract Ownership

The producing service owns the semantics
of its published contracts.

Consumers must not depend on internal
implementation classes.

Contracts may be located under:

    contracts/openapi/
    contracts/asyncapi/

The authoritative generation and publishing
workflow must be defined before implementation.

The repository must not maintain two conflicting
authoritative definitions of the same contract.

### TypeScript Clients

Frontend transport clients may be generated
from approved OpenAPI contracts.

Generated code must be reproducible.

Generated transport DTOs must not automatically
be treated as frontend domain models.

HTTP adapters should translate between
transport schemas and application models.

### Monetary Data

Monetary values must not depend on
binary floating-point precision.

HTTP contracts should represent monetary
amounts as decimal strings with explicit
currency information where necessary.

Frontend consumers must preserve numeric
precision required by the domain.

### Identifiers and Temporal Values

Identifiers must have stable external formats.

Date and time representations must specify
the expected precision and timezone semantics.

Timestamps should use unambiguous standardized
representations.

Business-local dates must not be confused
with absolute timestamps.

### Error Contracts

HTTP error responses should follow a consistent,
documented structure.

RFC 9457 Problem Details is the preferred
baseline for HTTP error representation.

Domain error identifiers must remain separate
from HTTP-specific status mappings.

### Versioning

Contract evolution must distinguish between:

- Backward-compatible additions.
- Deprecations.
- Breaking changes.

Breaking changes require an explicit
compatibility and migration strategy.

Consumer compatibility must not be assumed
merely because producer and consumer code
live in the same monorepo.

### Security

Contracts must not expose private
domain information unnecessarily.

Authentication and authorization requirements
must be explicit for protected operations.

Public APIs must define their permitted
information projections.

### Testing

Contract verification should include:

- Schema validation.
- Producer conformance.
- Consumer compatibility.
- Generated client compilation.
- Error response compatibility.

## Alternatives

### Shared Java Domain Libraries Across Services

Rejected as an integration contract mechanism.

### Handwritten Unversioned HTTP Clients

Rejected as the default approach where
machine-readable API contracts are available.

### Generated API DTOs as Domain Objects

Rejected because transport and domain
responsibilities are different.

## Consequences

Positive:

- Explicit service boundaries.
- Better client generation.
- Improved compatibility governance.
- Discoverable integrations.
- Independent deployment support.

Negative:

- Contract maintenance.
- Code generation configuration.
- Compatibility testing requirements.
- Schema evolution complexity.

## Open Decisions

- Contract-first versus code-first generation.
- Client generator selection.
- Kafka serialization format.
- Schema registry.
- Breaking-change migration policy.
- Contract artifact publication strategy.

## Final Decision

Service integration must use explicit,
versioned contracts with defined ownership.

OpenAPI is the preferred HTTP contract format.

This decision is accepted.
