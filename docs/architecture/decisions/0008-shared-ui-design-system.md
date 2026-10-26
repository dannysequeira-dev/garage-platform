# ADR-0008: Shared UI and Design System

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Frontend presentation reuse
- **Related:** ADR-0007

## Context

Garage Platform requires consistent presentation
across multiple functional domains.

Future applications may target different platforms.

Duplicating UI behavior and visual conventions
across applications would increase maintenance cost.

However, platform independence must not force
incompatible web and native UI abstractions.

## Decision

Adopt a reusable design system with shared
design tokens and platform-appropriate
UI component packages.

### Design Tokens

Design tokens should define:

- Colors.
- Typography.
- Spacing.
- Border radii.
- Elevation and shadows.
- Motion conventions.
- Breakpoints where appropriate.
- Semantic status colors.

Token definitions should remain independent
of any specific UI framework where practical.

### Shared UI Packages

Candidate packages include:

    shared/design-tokens/
    shared/ui-web/
    shared/ui-native/

Additional packages may be introduced when
a meaningful responsibility exists.

Package names and implementation libraries
remain subject to frontend design review.

### UI Primitives

Reusable primitives may include:

- Button.
- Input.
- FormField.
- Dialog.
- Select.
- Table.
- Card.
- Badge.
- Tabs.
- Navigation primitives.
- Feedback and error components.

Primitives must remain business-domain agnostic.

### Domain Presentation

Components such as:

- VehicleCard.
- MaintenanceTimeline.
- InventoryReservationStatus.

should remain in their owning domain's
presentation package when appropriate.

They must not be moved into shared UI merely
because they are used by multiple screens
within the same domain.

### Web and Desktop

Compatible web and desktop applications may
reuse React-based UI packages.

The application framework must not become
a dependency of frontend Core or Application.

### Mobile

Native applications may require separate
component implementations.

Shared tokens and interaction conventions
should provide consistent product identity.

Visual consistency must not require
unrealistic cross-platform abstractions.

### Accessibility

Shared UI should support:

- Keyboard navigation.
- Semantic interactions.
- Focus management.
- Accessible labeling.
- Error communication.
- Appropriate color contrast.
- Reduced-motion preferences.

Accessibility must be validated in the
actual target platform.

### Documentation

Components should have documented:

- Intended usage.
- Supported states.
- Accessibility behavior.
- Extension points.
- Examples.

Storybook may be introduced for web component
documentation and visual review.

Its adoption is not yet mandatory.

### Dependency Rules

Core and Application must not import UI packages.

Shared UI packages must not contain
authoritative business rules.

Domain-specific presentation may consume
shared UI primitives.

## Alternatives

### Duplicate Components Per Domain

Rejected as the default because it creates
inconsistent interactions and duplicated work.

### Universal UI Implementation for Every Platform

Rejected because different platform models
may require different implementations.

### One Large Shared Components Package

Discouraged when it mixes generic primitives
with business-domain presentation.

## Consequences

Positive:

- Consistent interface design.
- Reusable components.
- Reduced presentation duplication.
- Faster application development.
- Stronger accessibility governance.

Negative:

- Design system maintenance.
- Shared package compatibility concerns.
- Possible overgeneralization.
- Cross-platform implementation differences.

## Open Decisions

- Styling technology.
- Component primitives and libraries.
- Theme design.
- Mobile UI technology.
- Component documentation tooling.
- Visual regression strategy.

## Final Decision

Garage Platform adopts shared design tokens
and reusable presentation components while
preserving framework-independent application logic.

This decision is accepted.
