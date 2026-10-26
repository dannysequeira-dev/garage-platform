# Frontend Agent Instructions

**Scope:** All files under frontend/

These instructions extend the root AGENTS.md.

Read the architecture constitution and relevant ADRs
before implementing frontend changes.

## Technology

- TypeScript.
- pnpm Workspaces.
- Next.js for the initial web application.
- Framework-independent Core and Application packages.
- Shared design tokens and UI components.

Do not introduce new frameworks without justification.

## Fundamental Rule

Reusable application behavior must not depend
on a presentation framework.

The framework depends on the application,
not the application on the framework.

## Core

Core must remain framework-independent.

Forbidden dependencies include:

- React.
- Next.js.
- React Router.
- Expo.
- Electron.
- Tauri.
- Browser-specific APIs.

Prefer immutable models and pure functions.

Do not duplicate authoritative backend
business invariants.

## Application

Application owns reusable use cases
and interaction coordination.

External dependencies must be defined
through ports.

Use cases must be testable with fake
or in-memory adapters.

Avoid use-case classes that add no meaningful
behavior beyond forwarding a single call.

## Adapters

Adapters implement application ports.

HTTP, SSE, WebSocket, and platform storage
belong in appropriate adapter packages.

Transport DTOs must not automatically become
Core or Application models.

Do not connect frontend applications
directly to Kafka.

## Presentation

Framework-specific logic belongs in
Presentation or application composition modules.

React hooks must not be placed in Core.

Next.js routing and server components
must not become application dependencies.

Keep framework integration separate
from reusable behavior.

## Shared UI

Reuse design tokens and UI primitives.

Keep generic visual components separate
from domain-specific presentation components.

Do not move business behavior into shared UI.

Do not force web-specific UI implementations
onto native platforms.

Accessibility is a required quality consideration.

## Application Composition

Each application owns its Composition Root.

Dependencies should be assembled explicitly.

Authentication context must be scoped correctly,
especially during server-side rendering.

Never store request-specific credentials
in shared global singletons.

## State Management

Distinguish server state from local interaction state.

Framework-specific state libraries must remain
outside Core and Application.

Avoid duplicating remote server state
without a clear purpose.

## Contracts

Backend communication uses versioned contracts.

Generated HTTP clients belong behind adapters.

Do not manually duplicate API schemas
when an approved generated contract is available.

Respect backend authority for security,
inventory, ownership, maintenance, and money.

## Testing

Verify:

- Framework independence.
- Application use cases.
- Adapter contracts.
- State transitions.
- Shared UI behavior.
- Accessibility when applicable.

Use automated dependency checks to prevent
forbidden imports and circular dependencies.

## Implementation Protocol

Before changes:

1. Identify the affected frontend domain.
2. Read the relevant ADRs.
3. Determine whether behavior belongs in Core,
   Application, Adapters, Presentation, or App.
4. Review the existing API contracts.
5. Identify reusable components.

After changes:

1. Report affected packages.
2. Run applicable checks when available.
3. Explain new dependencies.
4. Update documentation when necessary.
5. Report any unverified behavior.

Do not scaffold unrelated applications.

Do not commit, push, or deploy without authorization.
