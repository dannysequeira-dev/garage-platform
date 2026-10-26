# ADR-0001: Single Monorepo with Independent Build Ecosystems

- **Status:** Accepted
- **Date:** 2026-10-26
- **Decision Makers:** Project Maintainer
- **Scope:** Repository organization, build isolation, and delivery boundaries
- **Related Documents:**
  - `README.md`
  - `AGENTS.md`
  - `docs/architecture/constitution.md`
  - `docs/architecture/system-overview.md`

---

## 1. Context

Garage Platform is designed as a distributed vehicle lifecycle
management platform.

The backend consists of independently deployable Java
microservices following Domain-Driven Design, Hexagonal
Architecture, and Event-Driven Architecture.

The frontend consists of TypeScript packages and applications
designed to share framework-independent application behavior
across web, mobile, and desktop platforms.

The platform also requires:

- Versioned API and messaging contracts.
- Shared architectural documentation.
- Consistent engineering conventions.
- Independent build and deployment pipelines.
- Infrastructure configuration.
- Coordinated changes across backend and frontend.
- AI-assisted development with accessible project context.

The project is initially maintained by a single developer.

Its architecture must support future commercialization
without prematurely requiring multiple repositories,
teams, or release processes.

## 2. Problem

A repository strategy must balance:

1. Independent service development.
2. Cross-system architectural consistency.
3. Easy discovery of contracts and documentation.
4. Build performance.
5. Independent deployment.
6. Dependency management.
7. AI-assisted development.
8. Long-term maintainability.

The repository structure must not create artificial
runtime coupling between otherwise independent systems.

## 3. Decision

Garage Platform SHALL use a single Git monorepo.

The repository SHALL contain both backend and frontend,
along with integration contracts, infrastructure,
and architectural documentation.

The monorepo is an organizational boundary,
not a runtime or deployment boundary.

Backend and frontend SHALL retain independent
build ecosystems.

Microservices and applications SHALL remain
independently buildable and deployable.

## 4. Repository Structure

The initial repository structure is:

    garage-platform/
    |
    |-- AGENTS.md
    |-- CLAUDE.md
    |-- README.md
    |
    |-- backend/
    |   |-- pom.xml
    |   |-- common/
    |   `-- services/
    |
    |-- frontend/
    |   |-- package.json
    |   |-- pnpm-workspace.yaml
    |   |-- apps/
    |   |-- domains/
    |   |-- platform/
    |   `-- shared/
    |
    |-- contracts/
    |   |-- openapi/
    |   `-- asyncapi/
    |
    |-- infra/
    |
    |-- docs/
    |   |-- architecture/
    |   |   `-- decisions/
    |   `-- domains/
    |
    `-- .github/
        `-- workflows/

This structure represents intended organization.

Directories and modules SHALL be introduced
incrementally when required.

The repository SHALL NOT be fully scaffolded
before its corresponding implementation scope
has been approved.

## 5. Backend Build Strategy

The backend SHALL use:

- Java.
- Maven.
- Parent POM inheritance.
- Maven multi-module aggregation.

The backend root POM SHALL manage shared
dependency and plugin conventions.

Individual services MAY define their own
parent and aggregator POMs.

Each service SHALL produce its own
independently deployable artifact.

The Maven reactor MAY build a service together
with its required internal dependencies.

Services MUST NOT depend on another service's
internal domain or infrastructure implementation.

Shared backend modules MUST contain
explicitly approved reusable abstractions.

A shared Maven parent POM MUST NOT imply
a shared runtime or deployment lifecycle.

## 6. Frontend Build Strategy

The frontend SHALL use:

- TypeScript.
- pnpm workspaces.
- Independent application packages.
- Domain-oriented reusable packages.
- Shared presentation and UI packages.

The initial web application SHALL use Next.js.

Future applications MAY include:

- Mobile applications.
- Desktop applications.
- Additional web applications.

Reusable frontend domain and application
packages MUST remain framework-independent.

React, Next.js, and other presentation
frameworks SHALL NOT be dependencies of
framework-independent core packages.

Framework-specific presentation packages
MAY be shared between compatible applications.

Shared design tokens MAY support multiple
platform-specific UI implementations.

The final build orchestration technology,
if any, remains an independent decision.

## 7. Dependency Boundaries

Repository proximity SHALL NOT imply
architectural dependency permission.

### Backend

- Domain Core MUST NOT depend on infrastructure.
- Domain Service MUST depend on ports,
  not concrete adapters.
- Infrastructure adapters implement
  domain-defined contracts.
- Services MUST NOT access other services'
  databases directly.
- Shared libraries MUST NOT introduce
  cross-context domain coupling.

### Frontend

- Core MUST remain framework-independent.
- Application logic MUST depend on abstractions.
- Infrastructure adapters implement
  application-defined ports.
- Presentation packages MAY depend on frameworks.
- Applications assemble dependencies through
  composition roots.
- Cross-domain imports MUST respect
  documented ownership boundaries.

Dependency rules SHOULD be enforced through
automated architecture checks.

## 8. Contract Management

The monorepo SHALL provide a central
location for integration contracts.

Synchronous service APIs SHOULD use OpenAPI.

Asynchronous integrations SHOULD use
versioned event schemas.

AsyncAPI MAY describe messaging interfaces.

Generated TypeScript clients MAY be produced
from approved OpenAPI contracts.

Generated clients MUST remain isolated
from framework-independent domain models.

A contract change MUST be reviewed for
compatibility with existing consumers.

Co-location of producer and consumer code
MUST NOT justify breaking existing contracts.

The authoritative source, generation process,
and publishing workflow for each contract
shall be defined in subsequent decisions.

## 9. Independent Deployment

Each deployable backend service and
frontend application SHALL have an
independent deployment lifecycle.

A change in one module MUST NOT automatically
require redeploying the entire platform.

Deployments SHALL be based on actual
runtime dependencies and compatibility
requirements.

A shared repository commit does not imply
that all components must use the same
runtime artifact version.

The exact release versioning strategy
remains an open decision.

## 10. Continuous Integration

CI pipelines SHOULD support:

- Independent backend builds.
- Independent frontend builds.
- Affected-module detection.
- Dependency-aware verification.
- Contract compatibility checks.
- Architecture boundary verification.
- Independent container image creation.
- Independent deployment workflows.

Changes to shared modules MUST trigger
verification of affected consumers.

Path-only change detection MUST NOT ignore
transitive dependencies.

Full repository verification MAY run
periodically or at defined integration gates.

Build optimization MUST NOT compromise
correctness or contract compatibility.

## 11. Development Environment

Local development SHOULD allow individual
services and applications to run independently.

Infrastructure dependencies MAY be provided
through Docker Compose or equivalent tooling.

Developers SHOULD be able to run a focused
subset of the platform.

A complete distributed environment MAY be
available for integration and system testing.

Local development MUST NOT require running
every service when the affected functionality
does not depend on them.

## 12. AI-Assisted Development

The monorepo SHALL maintain version-controlled
engineering instructions and architecture documents.

The root AGENTS.md defines shared
engineering expectations.

Tool-specific instruction files MAY reference
the shared instructions.

Nested instruction files MAY provide
module-specific guidance.

AI agents SHOULD:

1. Identify the affected bounded context.
2. Read the relevant architecture documentation.
3. Inspect existing contracts.
4. Respect dependency boundaries.
5. Make focused changes.
6. Run applicable verification.
7. Report unresolved architectural questions.

AI agents MUST NOT assume that all repository
contents are automatically loaded into context.

Documentation must be discoverable, concise,
and organized by responsibility.

The repository SHALL NOT rely on conversational
memory as its sole architectural documentation.

## 13. Alternatives Considered

### Alternative A: Separate Repository Per Microservice

Advantages:

- Strong repository isolation.
- Independent access permissions.
- Separate repository histories.

Disadvantages:

- Increased repository administration.
- More difficult coordinated changes.
- Distributed architectural documentation.
- Additional overhead for a single maintainer.

**Decision:** Rejected for the initial platform.

### Alternative B: Separate Backend and Frontend Repositories

Advantages:

- Clear separation between technology stacks.
- Independent repository permissions.
- Smaller repositories.

Disadvantages:

- Cross-stack changes require multiple repositories.
- Contract coordination becomes more complex.
- Architecture context is fragmented.
- Additional maintenance overhead.

**Decision:** Rejected for the initial platform.

### Alternative C: One Monorepo with One Unified Build Tool

Advantages:

- Centralized build orchestration.
- Potentially simpler top-level commands.

Disadvantages:

- Additional coupling between ecosystems.
- Extra build tooling and configuration.
- Unnecessary abstraction over Maven and pnpm.

**Decision:** Not selected as the initial approach.

Build orchestration MAY be introduced later
without replacing the underlying build systems.

## 14. Positive Consequences

The decision provides:

- Centralized architectural documentation.
- Shared visibility of system contracts.
- Easier coordinated changes.
- Consistent engineering conventions.
- Easier cross-stack architectural reviews.
- Improved context discovery for AI assistants.
- Independent runtime deployments.
- Independent backend and frontend tooling.
- Simplified initial repository administration.

## 15. Negative Consequences

The decision introduces:

- Potential repository growth.
- More complex CI dependency analysis.
- Increased importance of module boundaries.
- Shared repository permissions.
- Potentially broader impact from shared changes.
- Larger search and indexing scopes.
- Additional responsibility for preventing
  unintended cross-module dependencies.

These consequences are accepted.

They must be mitigated through:

- Architecture tests.
- Module ownership conventions.
- Affected-module CI.
- Contract verification.
- Focused documentation.
- Independent build validation.
- Appropriate secret and access management.

## 16. Verification Criteria

This decision is successfully implemented when:

1. Backend can be built through Maven without
   requiring frontend tooling.

2. Frontend can be built through pnpm without
   requiring Maven.

3. Individual microservices can produce
   independent deployment artifacts.

4. Individual frontend applications can produce
   independent deployment artifacts.

5. Dependency boundaries are checked automatically.

6. API and event contracts are discoverable
   and version-controlled.

7. A cross-stack feature can be reviewed
   within one repository change.

8. Architectural documentation can be located
   without relying on prior conversations.

These criteria will be verified progressively
as implementation modules are introduced.

## 17. Open Questions

The following remain unresolved:

- Exact CI/CD provider and workflow implementation.
- Artifact versioning strategy.
- Release tagging conventions.
- Container registry and deployment environment.
- Optional monorepo build orchestration.
- Contract generation and publication tooling.
- Dependency graph analysis implementation.
- Production repository access controls.

These questions require separate decisions
when their implementation becomes necessary.

## 18. Reconsideration Conditions

This decision SHOULD be revisited if:

- Independent teams require incompatible
  repository access policies.
- Regulatory or contractual isolation
  requirements emerge.
- Repository scale materially degrades
  development or CI performance.
- Deployment governance requires
  stronger repository separation.
- Organizational boundaries justify
  independently governed repositories.

Any change requires a new ADR that supersedes
this decision.

## 19. Final Decision

Garage Platform SHALL use one Git monorepo
with independently governed build,
dependency, and deployment boundaries.

Backend SHALL use Maven.

Frontend SHALL use pnpm workspaces.

Shared repository ownership SHALL NOT
compromise microservice autonomy or
frontend framework independence.

This decision is accepted.
