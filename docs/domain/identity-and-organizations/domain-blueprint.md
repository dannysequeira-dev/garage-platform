# Identity, Organizations, and Vehicle Authorization - Candidate Domain

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Scope
Authentication, membership and resource authorization are essential but their **bounded context placement remains Draft** under ADR-0010. This blueprint describes semantics, not a committed service boundary.

## Concepts
`UserAccount` (auth identity), `Organization` (workshop/fleet business), `Membership` (role within organization), `Party` (legal subject, potentially external), `VehicleAccessGrant` (scoped resource permission), `ConsentRecord` (privacy/legal), `Role` (permission grouping), `PolicyDecision` (allow/deny with reason and policy version).

## Candidate permissions
- `vehicle:read`, `vehicle:edit`, `vehicle:ownership:manage`.
- `maintenance:read`, `maintenance:create`, `maintenance:complete`, `maintenance:correct`.
- `inventory:read`, `inventory:reserve`, `inventory:adjust`.
- `schedule:read`, `schedule:manage`.
- `qr:issue`, `qr:revoke`.
- `organization:member:manage`, `audit:read`.
Permission names are proposals; authorization model should avoid wildcard administrator leakage.

## Decision semantics
Input `(principal, action, resource, organization context, relevant grants, policy)` -> explicit allow/deny. Current verified legal ownership might influence eligibility but must not be equated to a login or automatically grant historical workshop-document access.

## Tenant boundary
Evaluate globally stable VehicleId with org-scoped access grants versus tenant-scoped vehicle records. Shared database/schema with row-level security is an **option**, not accepted architecture. Service-level authorization remains mandatory regardless of DB mechanism.

## High-risk workflows
Add a member with access to many workshop customers; owner transfers vehicle but keeps another active co-owner; revoke caretaker access; service advisor accesses another workshop's invoice; support/admin emergency access; QR field policy after owner transfer.

## Review required
Identity provider (OIDC), legal Party system of record, resource access graph, consent, tenant persistence, audit retention and incident support access. Refer to `docs/architecture/security/*`.
