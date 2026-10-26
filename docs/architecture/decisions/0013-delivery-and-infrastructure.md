# ADR-0013: Delivery and Infrastructure Strategy

- **Status:** Draft
- **Date:** 2026-10-26
- **Scope:** Builds, releases, runtime infrastructure
- **Related:** ADR-0001, ADR-0002

## Context

Garage Platform uses one Git monorepo
containing independently deployable
Java services and TypeScript applications.

The platform requires repeatable builds,
isolated configuration, reliable migrations,
and automated verification.

The exact production topology has not
yet been approved.

## Confirmed Constraints

The repository uses:

- Maven with parent POMs for Java.
- pnpm Workspaces for TypeScript.
- Independent microservice artifacts.
- Independent frontend application artifacts.
- Version-controlled infrastructure definitions.
- Explicit integration contracts.

A monorepo must not imply a shared
deployment lifecycle.

## Proposed Delivery Model

### Backend Builds

Each Java service should support:

- Independent Maven verification.
- Unit and integration testing.
- Architecture tests.
- Dependency analysis.
- Deployable Spring Boot packaging.
- Container image creation.

The executable container module should
own runtime application assembly.

### Frontend Builds

Each frontend application should support:

- Independent type checking.
- Linting.
- Unit and integration tests.
- Production compilation.
- Application-specific packaging.

Reusable packages must be verified
according to their dependency graph.

### Affected-Module Detection

CI should detect changed modules.

Changes to shared modules must trigger
verification of affected consumers.

Path-based detection alone must not ignore
transitive dependencies.

Periodic full-repository validation
may complement focused pipelines.

### Containerization

Container images should be independently built
and versioned for each deployable unit.

Images should:

- Avoid unnecessary runtime tooling.
- Run with appropriate permissions.
- Exclude development secrets.
- Include suitable health checks.
- Support reproducible builds.

### Local Development

Docker Compose is a candidate for the
local infrastructure environment.

The local stack may include:

- PostgreSQL.
- Kafka.
- Required service dependencies.
- Observability components as needed.

Developers should be able to run only
the services relevant to their task.

### Production Orchestration

Candidate deployment models include:

- Docker Compose on managed hosts.
- Container orchestration platforms.
- Kubernetes.
- Managed container services.

No final production orchestrator
is approved yet.

### Configuration

Applications must obtain environment-specific
configuration through controlled mechanisms.

Secrets must not be committed to Git.

Configuration must remain separate from
compiled business behavior.

### Database Migrations

Each service owns its database schema
and migration lifecycle.

Flyway is the preferred candidate
for relational database migrations.

No service may execute migrations
against another service's database.

Migration compatibility must be evaluated
for independent service rollouts.

### Release Compatibility

Services and applications may deploy
at different times.

Public integration contracts must remain
compatible with supported consumers.

Database migrations should support
safe rollout strategies when necessary.

### CI/CD Security

Pipelines should implement:

- Least-privilege credentials.
- Dependency and vulnerability checks.
- Secret protection.
- Artifact provenance where practical.
- Controlled production deployments.

### Disaster Recovery

Production deployment must eventually define:

- Database backup.
- Restore testing.
- Service recovery.
- Kafka retention and recovery.
- Recovery objectives.
- Infrastructure reconstruction.

## Alternatives to Evaluate

### Kubernetes From Day One

Benefits:

- Extensive orchestration capabilities.
- Standard deployment primitives.

Costs:

- Significant operational overhead.
- Additional infrastructure complexity.

### Compose-Based Initial Production

Benefits:

- Simpler operations.
- Lower initial administration overhead.

Costs:

- More limited orchestration.
- Different scaling and recovery characteristics.

### Managed Container Platform

Benefits:

- Reduced infrastructure management.

Costs:

- Provider-specific integration.
- Potential platform restrictions.

## Open Decisions

- Production hosting provider.
- Production orchestrator.
- CI/CD provider.
- Image registry.
- Artifact versioning.
- Environment topology.
- Secret management.
- Deployment promotion strategy.
- Database migration tooling.
- Backup and recovery objectives.

## Acceptance Criteria

A final decision must demonstrate:

- Independent builds.
- Independent deployments.
- Contract compatibility validation.
- Repeatable infrastructure provisioning.
- Secure configuration.
- Recovery procedures.
- Clear operational costs.

## Status

Draft.

Existing repository and build-system
decisions remain accepted.

The production runtime topology
remains unresolved.
