# Design System, Accessibility, and UI Component Contracts

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Goals
Shared semantic tokens, consistent interactions and accessible web components, while respecting web/native differences. ADR-0008 Accepted; concrete library/styling choices remain open.

## Token layers
- **Primitive:** neutral palette, spacing scale, type families/weights, radius and elevation.
- **Semantic:** surface, text, border, focus ring, success/warning/danger, maintenance-due status.
- **Component:** button heights, input errors, dialog spacing, table density, touch targets.
- **Platform:** CSS variables for web; equivalent typed token consumption for future native UI.

## Candidate UI primitives
Button, IconButton, TextInput, Select, DateTimeField, Number+UnitField, Dialog, Drawer, Toast, Alert, Table, Pagination, Badge, Tabs, StatusTimeline, LoadingSkeleton, EmptyState and AccessibleErrorSummary.

## Domain-specific presentations
VehicleCard, OwnershipTimeline, ServiceRecordCard, DueIndicator, StockReservationState, QRPreview belong to domain presentation packages, not generic shared primitives.

## Accessibility contract
Keyboard-only flows, visible focus, labels, escape handling in dialogs, form error announcement, contrast verification, reduced motion, responsive text, assistive tech testing. Select the target WCAG level during NFR approval; avoid unverified compliance claims.

## State representations
All critical operations need variants: pending, success-confirmed, error, retryable, disabled due to rights, and stale/synchronizing. Status badges must not rely solely on color; use textual meaning and announce updates appropriately.

## Data formatting
Money (explicit currency), kilometer/mile, local dates vs absolute instants, numeric precision, privacy masking and copyable identifiers must follow a central formatting convention separate from pure domain values.

## Candidate documentation tools
Storybook and visual regression are optional. Exact CSS framework, component primitives, icon system and typography are not approved. The design system must remain independent of core business validation.

## Review checklist
Verify tokens, dark/light mode intent, typography, cross-platform consistency, input parsing, screen reader tests, QR print readability, touch sizing and error/empty states against screen catalog.
