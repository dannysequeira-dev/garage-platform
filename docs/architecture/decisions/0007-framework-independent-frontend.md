# ADR-0007: Framework-Independent Frontend Architecture

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** TypeScript domain and application architecture
- **Related:** ADR-0001, ADR-0002

## Context

Garage Platform initially requires a web application.

Future applications may target mobile and desktop.

Application behavior must not need to be rewritten
when the presentation framework or platform changes.

The frontend must also avoid duplicating the
authoritative business rules implemented in Java.

## Decision

Adopt a TypeScript frontend monorepo based on
domain-oriented packages, Hexagonal Architecture,
and framework-independent application logic.

pnpm Workspaces is the workspace manager.

Next.js is the initial web application.

### Conceptual Organization

    frontend/
        apps/
            web/
            mobile/
            desktop/

        domains/
            vehicles/
            maintenance/
            inventory/
            scheduling/

        platform/
        shared/

Each frontend domain may contain:

    core/
    application/
    adapters/
    presentation/

This layout is a reference structure.

Package boundaries may be refined without
changing the architectural dependency rules.

### Core

Core contains framework-independent models,
Value Objects, pure transformations, and
client-relevant behavior.

Core must not depend on:

- React.
- Next.js.
- React Router.
- Expo.
- Electron.
- Tauri.
- Platform-specific browser APIs.

Core must not duplicate all authoritative
backend aggregate behavior.

### Application

Application contains reusable use cases,
interaction workflows, and external dependency
contracts.

Application must remain framework-independent.

It defines outbound ports required for:

- Remote data access.
- Realtime updates.
- Platform storage where appropriate.
- Other external application capabilities.

Application may depend on Core and approved
framework-independent utilities.

### Adapters

Adapters implement the corresponding ports.

Examples:

- HTTP adapters.
- SSE adapters.
- WebSocket adapters.
- Local persistence adapters.
- Platform-specific integrations.

Adapters translate external representations
into appropriate application models.

Generated API clients should remain behind
these integration boundaries.

### Presentation

Presentation contains framework-specific
integration and rendering behavior.

Examples:

- React hooks.
- Query library integration.
- Components.
- Framework state providers.
- Route-level presentation composition.

Presentation may depend on framework-specific
libraries.

It must not force those dependencies into
Core or Application.

### Composition Root

Each deployable application owns its
Composition Root.

It selects and assembles:

- Application use cases.
- Infrastructure adapters.
- Authentication integrations.
- Presentation integrations.
- Platform-specific dependencies.

Dependency injection must be explicit enough
to support isolated tests and alternative
platform implementations.

### Backend Authority

The backend remains authoritative for:

- Vehicle ownership rules.
- Maintenance due calculations.
- Inventory reservations.
- Financial decisions.
- Durable business state.
- Authorization.

The frontend may own:

- Form interaction workflows.
- Client-side validation for usability.
- Local selection state.
- Filtering and presentation transformations.
- Client-side orchestration.
- Loading and error states.

Client-side checks must not be treated
as authoritative business validation.

### State Management

Remote server state should use an appropriate
query/cache mechanism.

Local interaction state may use a dedicated
state management library when justified.

Framework-specific state libraries must remain
outside Core and Application.

### Application Portability

A reusable application use case must be
executable in a Node-based test without
starting React, Next.js, or a browser.

Different applications may provide different
implementations of the same ports.

Shared behavior does not require sharing
every visual component or screen.

### Server Rendering

Next.js server-side execution must respect
request-specific authentication context.

Authenticated clients must not store
user-specific credentials in global
cross-request singletons.

### Realtime

Kafka must remain backend-only.

Frontend realtime access must use an approved
backend interface such as SSE or WebSocket.

Realtime mechanisms must be hidden behind
application-facing contracts.

## Alternatives

### Next.js-Only Application Architecture

Rejected because reusable behavior would
become coupled to a single framework.

### Duplicate Logic Per Client Application

Rejected because it increases maintenance
costs and inconsistent behavior.

### Shared React Components as the Entire Core

Rejected because behavior must remain portable
beyond React-specific presentation packages.

## Consequences

Positive:

- Portable application behavior.
- Framework-independent testing.
- Platform flexibility.
- Consistent interaction logic.
- Explicit integration boundaries.

Negative:

- Additional packages.
- Composition and adapter code.
- Mapping between API and application models.
- Stronger dependency governance requirements.

## Verification

Checks must verify:

- No framework imports in Core or Application.
- Use cases execute with in-memory adapters.
- HTTP adapters satisfy application ports.
- Framework-specific dependencies are isolated.
- No direct Kafka connection from frontend.
- No duplicate backend authority.

## Final Decision

Frontend business interaction and reusable
application behavior must remain independent
of presentation frameworks.

This decision is accepted.
