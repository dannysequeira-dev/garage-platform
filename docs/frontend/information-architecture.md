# Frontend Information Architecture and Navigation

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Design intent
Web is first application (Next.js), with reusable TypeScript Core/Application packages and optional future mobile/desktop. UI navigation is role-sensitive but **never** the security boundary; APIs authorize each resource independently.

## Proposed route families (logical, not approved exact routes)
```text
/auth                   sign-in, session handling
/garage                 personal vehicle list and overview
/garage/vehicles/:id    details, history, odometer, maintenance plans
/garage/vehicles/:id/qr public grant management (authorized)
/workshop              organization dashboard
/workshop/orders       service order operations
/workshop/schedule     availability and appointments
/workshop/inventory    parts, stock and reservations
/workshop/team         membership and roles
/public/v/:publicId    scoped public QR view
/settings              profile, preferences, privacy and grants
```
Exact routes are subject to framework, tenant, session and QR token security review.

## Information model
- Primary 'My Garage': vehicle cards with make/model, service due status, readable ownership/access descriptor.
- Vehicle detail: identity, odometer freshness, plans, service history, authorized attachments, explicit source trust.
- Workshop: orders and appointments as operational queues; stock dashboard and audit.
- Sharing: QR field preview, secret issuance, revoke/rotate; share history by explicit grant separate from QR.
- Security: account, org membership, vehicle grants, export/delete requests (if supported).

## Cross-role navigation
Users may have multiple organizations and personal garage access; organization switching must not leak cached records across contexts. Use per-context cache keys, query authorization, active organization header, and clear session boundaries.

## State representation
Every asynchronous operation should communicate `pending`, `confirmed`, `failed`, `retryable`, or `needs attention`. A completed maintenance may appear immediately while Registry mileage is synchronizing. Do not imply all bounded contexts share one transaction.

## Accessibility and responsive behavior
Keyboard and screen-reader navigation, consistent focus behavior, responsive tables, touch targets, contrast, error summaries, motion preferences. Validate full workflows on small viewport; do not assume mobile app exists in V1.

## Review
Validate navigation against actors, use cases, resource grants, localization and screen catalog before implementing routes.
