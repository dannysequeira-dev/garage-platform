# ADR-0010: Identity, Authorization, and Multi-Tenancy

- **Status:** Draft
- **Date:** 2026-10-26
- **Scope:** Identity, organizations, authorization, data isolation
- **Related:** ADR-0002, ADR-0011

## Context

Garage Platform may eventually support
multiple individuals, workshops, and organizations.

A vehicle is not necessarily owned by the
organization currently providing its maintenance.

The platform must distinguish:

- Vehicle ownership.
- Platform user identity.
- Organization membership.
- Authorized access to records.
- Ownership of service documents.
- Tenant boundaries.

The exact commercial and tenancy model
is not yet approved.

## Proposed Direction

### Identity

Use a standards-based identity solution
rather than embedding authentication credentials
inside domain aggregates.

OpenID Connect and OAuth-based integration
should be evaluated.

Keycloak is a candidate, not an approved dependency.

### Authorization

Authorization remains authoritative
in backend services.

Permissions must be evaluated according to
the operation and relevant resource.

Role-based and attribute-based models may
be combined when appropriate.

A valid authentication token alone must not
grant unrestricted vehicle access.

### Vehicle Ownership

Vehicle ownership is a domain relationship.

It must not automatically imply:

- Platform user registration.
- Workshop organization membership.
- Access to all historical documents.
- Access to unrelated service records.

Ownership history may include simultaneous
owners and temporal relationships.

### Organizations

Organizations may represent:

- Workshops.
- Companies.
- Vehicle fleet operators.
- Other authorized business participants.

A platform user may belong to multiple
organizations.

An organization may manage multiple vehicles
without necessarily owning them.

### Multi-Tenancy

Candidate persistence strategies include:

1. Shared database with tenant-scoped rows.
2. Schema-per-tenant.
3. Database-per-tenant.
4. Hybrid isolation.

No specific model is approved yet.

All candidate strategies must address:

- Isolation.
- Authorization.
- Migration.
- Backup and recovery.
- Operational cost.
- Data portability.
- Cross-organization sharing.

### Global Vehicle Identity

A significant unresolved decision is whether
Vehicle Registry uses globally shared vehicle
identities or tenant-scoped vehicle records.

This decision affects:

- Vehicle registration.
- Ownership history.
- Service record access.
- Cross-workshop history.
- VIN uniqueness.
- Public QR profiles.

The system must not infer unrestricted
cross-tenant access from matching VIN values.

### Data Protection

Sensitive ownership and service information
must remain protected.

Business authorization and technical tenant
isolation must reinforce each other.

Relevant operations should generate
auditable security records.

### Public Access

Public QR permissions must be scoped
independently of private application access.

QR access grants do not replace authorization
for protected operations.

## Alternatives to Evaluate

### Shared Global Vehicle Registry

Benefits:

- Unified vehicle identity.
- Easier long-term technical history linking.

Risks:

- Complex authorization.
- Data-sharing governance.
- Privacy concerns.

### Tenant-Isolated Vehicle Registries

Benefits:

- Stronger organizational isolation.
- Simpler local permissions.

Risks:

- Duplicate vehicle representations.
- More difficult cross-organization history.

### Hybrid Identity Model

Benefits:

- Potential balance between global identity
  and scoped business records.

Risks:

- Greater model and operational complexity.

## Decisions Still Required

- Global versus tenant-scoped vehicle identity.
- Identity provider.
- Organization membership model.
- Authorization strategy.
- Tenant persistence strategy.
- Record-sharing policy.
- Historical ownership visibility.
- Audit and retention requirements.

## Acceptance Criteria

This ADR may be accepted when:

- The tenancy model is explicit.
- Resource ownership is defined.
- Cross-organization access is specified.
- Authorization requirements are testable.
- Data isolation can be verified.
- Migration and operational consequences
  have been evaluated.

## Status

Draft.

No specific tenancy storage strategy or
identity provider is approved by this ADR.

Existing constitutional security constraints
remain mandatory.
