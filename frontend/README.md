# Garage Platform — Frontend

The Garage Platform frontend is a TypeScript monorepo
designed around framework-independent application behavior,
Hexagonal Architecture, reusable presentation packages,
and independently deployable applications.

The frontend must support web, mobile, and desktop platforms
without requiring application behavior to be rewritten.

## Architecture

The frontend follows:

- Domain-oriented modular architecture.
- Hexagonal Architecture (Ports and Adapters).
- Framework-independent Core and Application modules.
- Dependency inversion.
- Shared design tokens and UI components.
- Explicit application composition roots.
- Typed API contracts.
- Backend-owned authoritative business rules.

## Technology

- TypeScript.
- pnpm Workspaces.
- Next.js for the initial web application.
- React for compatible presentation packages.
- Generated HTTP clients from OpenAPI contracts.

Additional libraries will be selected through
explicit technical decisions.

## Planned Repository Structure

    frontend/
    ├── package.json
    ├── pnpm-workspace.yaml
    ├── tsconfig.base.json
    │
    ├── apps/
    │   ├── web/
    │   ├── mobile/
    │   └── desktop/
    │
    ├── domains/
    │   ├── vehicles/
    │   ├── maintenance/
    │   ├── inventory/
    │   ├── scheduling/
    │   └── public-access/
    │
    ├── shared/
    │   ├── design-tokens/
    │   ├── ui-web/
    │   └── ui-native/
    │
    └── platform/
        ├── api-contracts/
        ├── authentication/
        └── observability/

These directories will be created incrementally.

## Domain Organization

A typical frontend domain contains:

    <domain>/
    ├── core/
    ├── application/
    ├── adapters/
    │   ├── http/
    │   └── realtime/
    └── presentation/
        └── react/

These responsibilities may be represented
by independent workspace packages.

Package boundaries must follow actual
reuse and dependency requirements.

## Core

Core contains framework-independent models
and pure behavior.

Examples:

- Value Objects.
- Read models.
- Pure transformations.
- Interaction-related domain models.
- Domain-specific error representations.

Core must not depend on React, Next.js,
browser APIs, or platform-specific libraries.

Frontend models do not need to reproduce
every backend Aggregate Root.

## Application

Application contains reusable use cases
and interaction workflows.

It defines the ports required for external
operations, such as data access and realtime updates.

Application must not depend on:

- React.
- Next.js.
- React Router.
- Expo.
- Electron.
- Tauri.
- UI component packages.

Use cases should be independently testable
with in-memory implementations of their ports.

## Adapters

Adapters implement application-defined ports.

Examples include:

- HTTP clients.
- SSE subscriptions.
- WebSocket subscriptions.
- Local storage adapters.
- Platform-specific integrations.

Transport-specific concerns must remain
outside Core and Application.

Generated API clients should be used behind
appropriate transport adapters.

## Presentation

Presentation contains framework-specific
integration and visual behavior.

Examples:

- React hooks.
- Query library integrations.
- Components.
- Form presentation.
- View state adapters.

Presentation may depend on React or
other relevant framework libraries.

Reusable application behavior must not depend
on presentation implementations.

## Applications

Applications are Composition Roots.

Each application selects and configures:

- Application use cases.
- Infrastructure adapters.
- Authentication integrations.
- State management.
- Presentation packages.
- Platform-specific dependencies.

### Web

The initial web application uses Next.js.

Its responsibility is to render and coordinate
the user experience, not implement authoritative
backend business rules.

### Mobile

A future mobile application may use Expo.

It should reuse framework-independent Core
and Application packages.

Native UI may use platform-specific components.

### Desktop

A future desktop application may use Tauri
or another approved runtime.

It should reuse the same application behavior
where appropriate.

## Shared Design System

The frontend includes reusable design tokens
and platform-appropriate UI components.

Shared design tokens may define:

- Colors.
- Typography.
- Spacing.
- Radius.
- Elevation.
- Motion.
- Semantic states.

Shared UI components may include:

- Buttons.
- Inputs.
- Dialogs.
- Forms.
- Tables.
- Navigation primitives.
- Feedback components.

Business-domain components should remain
within their domain presentation packages
unless broader reuse is justified.

Web and native implementations may differ
while sharing design principles and tokens.

## State Management

Server state and interaction state are distinct.

Remote server state may use a query/cache library.

Local interaction state may use an appropriate
state management solution.

Framework-specific state libraries must not
become dependencies of Core or Application.

## Backend Integration

The Java backend is authoritative for:

- Business invariants.
- Ownership transitions.
- Maintenance due calculations.
- Inventory integrity.
- Financial business decisions.
- Authorization.
- Durable business state.

The frontend may implement client-side checks
for usability, but they do not replace backend
validation or authorization.

## API Contracts

HTTP integration should use versioned
OpenAPI contracts.

Generated clients must remain isolated
from framework-independent application models.

Adapters map transport DTOs into appropriate
frontend representations.

Monetary precision must be preserved across
the backend/frontend boundary.

## Realtime Integration

Frontend applications must not communicate
directly with Kafka.

Realtime communication may use SSE or WebSocket
through application-defined ports.

The transport mechanism must remain replaceable
without rewriting reusable use cases.

## Testing

Testing must cover:

- Pure Core behavior.
- Framework-independent application use cases.
- Adapter contracts.
- Interaction state.
- Shared UI components.
- Accessibility.
- Application integration.

Core and Application must be testable
without starting Next.js or React.

## Development

The frontend uses pnpm Workspaces.

Workspace initialization and build commands
will be documented when the first packages
are created.

## Architecture Documentation

See:

- ../docs/architecture/constitution.md
- ../docs/architecture/system-overview.md
- ../docs/architecture/decisions/

Relevant ADRs:

- ADR-0007 — Framework-Independent Frontend
- ADR-0008 — Shared UI and Design System
- ADR-0009 — Contract Governance
- ADR-0014 — Testing and Architecture Enforcement

## Current Status

Architecture foundation.

Frontend workspace packages have not yet
been initialized.
