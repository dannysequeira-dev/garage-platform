# Frontend Engineering Constitution

**Project:** Garage Platform
**Scope:** frontend/
**Version:** 0.1.0
**Status:** Draft

This document extends the project-wide Architecture Constitution.

The frontend is a collection of reusable TypeScript
application modules exposed through platform-specific
applications.

The frontend is not architecturally owned by Next.js.

## I. Framework Independence

Domain Core and Application MUST remain independent
of presentation frameworks.

They MUST NOT depend on React, Next.js, React Router,
Expo, Electron, Tauri, or framework-specific UI libraries.

Reusable behavior MUST be executable without
starting a presentation framework.

Framework dependencies MUST remain in presentation
packages and composition roots.

## II. Domain-Oriented Organization

Frontend code MUST be organized around meaningful
business capabilities.

Domain packages MAY contain:

- core/
- application/
- adapters/
- presentation/

Core MUST contain framework-independent models
and pure behavior.

Application MUST coordinate reusable use cases
and define external dependency ports.

Adapters MUST implement those ports.

Presentation MUST own framework-specific integration.

## III. Dependency Inversion

Application modules MUST depend on abstractions.

Infrastructure adapters MUST implement
application-defined contracts.

Application code MUST NOT directly depend on
concrete HTTP, storage, or realtime implementations.

Applications MUST assemble their dependencies
through explicit Composition Roots.

Cross-domain dependencies MUST respect
documented ownership boundaries.

## IV. Backend Authority

The backend MUST remain authoritative for:

- Business invariants.
- Financial business decisions.
- Inventory correctness.
- Ownership transitions.
- Maintenance due calculations.
- Authorization.
- Durable business state.

Frontend packages MAY implement:

- Interaction state.
- Presentation transformations.
- Form coordination.
- Client-side workflows.
- Optimistic interaction states.
- Remote data orchestration.

Client-side validation MUST NOT replace
backend business validation.

## V. Shared Design System

Reusable design tokens SHOULD be shared
between compatible applications.

Shared UI packages MAY contain:

- Buttons.
- Inputs.
- Dialogs.
- Tables.
- Forms.
- Typography.
- Accessibility utilities.
- Navigation primitives.

Business-specific components SHOULD remain
within their owning domain presentation package.

Shared UI MUST NOT introduce dependencies
into framework-independent Core or Application.

Cross-platform visual consistency MUST NOT
require identical component implementations.

## VI. State and Data Access

Server state and interaction state MUST remain
conceptually distinct.

Generated API clients MUST remain at
integration boundaries.

Transport DTOs MUST NOT automatically become
application domain models.

Frontend applications MUST NOT connect
directly to Kafka.

Realtime mechanisms MUST be exposed through
application-defined ports.

Authenticated server-side execution MUST
isolate credentials between user requests.

## VII. Portability

Web, mobile, and desktop applications SHOULD
reuse compatible application behavior.

Platform-specific requirements MUST be implemented
through adapters or presentation modules.

Reusable TypeScript use cases MUST be testable
with in-memory or fake adapters.

No application MUST be required to use Next.js
merely to reuse domain functionality.

## VIII. Verification

Architecture tests MUST prevent forbidden imports.

Core and Application tests MUST execute without
React or Next.js initialization.

Adapter implementations MUST be contract-tested.

Shared UI components SHOULD include accessibility
and interaction tests.

Application builds MUST remain independently executable.

## Governance

This document extends:

- docs/architecture/constitution.md
- docs/architecture/decisions/0007-framework-independent-frontend.md
- docs/architecture/decisions/0008-shared-ui-design-system.md
- docs/architecture/decisions/0009-contract-governance.md
- docs/architecture/decisions/0014-testing-and-architecture-enforcement.md

Changes affecting approved architectural guarantees
MUST follow the project amendment and ADR process.

Framework selection MUST NOT compromise the
portability guarantees defined here.
