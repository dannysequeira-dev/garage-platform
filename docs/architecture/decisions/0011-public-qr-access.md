# ADR-0011: Scoped and Revocable Public QR Access

- **Status:** Accepted
- **Date:** 2026-10-26
- **Scope:** Public vehicle profiles and QR security
- **Related:** ADR-0010

## Context

Garage Platform must allow authorized vehicle
information to be accessed by scanning a QR code.

A public QR may display:

- Vehicle identification.
- License plate when authorized.
- VIN when explicitly permitted.
- Last maintenance summary.
- Next maintenance due summary.

The QR must not grant unrestricted access
to application records or other vehicles.

## Decision

Public QR access uses a vehicle-scoped,
non-predictable, revocable access credential.

### Public Identification

Each publicly accessible vehicle may receive
a public identifier distinct from its
internal aggregate identifier.

Public identifiers do not grant access by themselves.

### Access Tokens

Access credentials must be generated using
a cryptographically secure random source.

Tokens must provide sufficient entropy
to prevent practical guessing.

Tokens must not be derived predictably from:

- Vehicle IDs.
- License plates.
- VINs.
- Owner identifiers.

The access token may be included in a QR URL
as an opaque bearer credential.

### Credential Storage

Raw QR access tokens must not be stored
in recoverable plaintext by default.

The database must store secure verification
material, such as a cryptographic token digest.

Token verification must use appropriate
security practices.

Credential revocation and rotation
must be supported.

### Public URL

A conceptual URL format is:

    /v/{publicVehicleId}/{accessToken}

The final route and hostname may change.

Only the designated Public Access service
may validate the corresponding credential.

### Public Projection

The QR endpoint returns a restricted
public vehicle projection.

It must not expose the full Vehicle aggregate.

Public projections may include:

- Vehicle make and model.
- Vehicle type.
- Authorized plate information.
- Authorized VIN information.
- Maintenance summary.
- Next maintenance summary.

The full VIN must not be exposed by default.

Owner contact information, private documents,
and internal service details remain private
unless a specific access policy permits them.

### Permission Changes

Access grants must support:

- Issuance.
- Revocation.
- Rotation.
- Expiration when configured.
- Scope modification when authorized.

Relevant ownership or authorization changes
must trigger policy reevaluation.

A QR profile must not retain information
that is no longer authorized for publication.

### Security Boundary

Possession of a QR token grants access
only to the explicitly permitted projection.

The token must not authorize:

- Vehicle modification.
- Maintenance record changes.
- Inventory operations.
- Access to other vehicles.
- Administrative operations.

### Operational Security

Public QR access should include:

- HTTPS.
- Rate limiting.
- Appropriate cache controls.
- Restricted referrer behavior.
- Minimal third-party resources.
- Protection against token disclosure in logs.
- Monitoring of suspicious access patterns.

A copied QR code remains a copied bearer credential.

Hashing its database representation does not
prevent someone from sharing the QR image.

Public information must therefore be selected
with that exposure model in mind.

### Integration

Public Access owns:

- Credential lifecycle.
- Access validation.
- Public vehicle projection.

Vehicle Registry and Maintenance remain
authoritative for their respective business data.

Public projections may be updated
asynchronously from Integration Events.

Eventual consistency must be considered
when projecting or revoking information.

## Alternatives

### Predictable Vehicle URL Only

Rejected because identifiers alone must not
provide privileged access.

### Full Vehicle API Through QR

Rejected because it exposes unrelated
internal and private information.

### Raw Token Database Storage

Rejected as the default storage policy.

## Consequences

Positive:

- Controlled information sharing.
- Revocable public access.
- Isolation from internal aggregates.
- Useful physical QR integration.

Negative:

- Credential lifecycle management.
- Projection synchronization.
- Token leakage considerations.
- Privacy policy complexity.

## Verification

Tests must verify:

- Invalid token rejection.
- Revoked token rejection.
- Credential rotation.
- Vehicle-level isolation.
- Authorized field projections.
- No private record exposure.
- No cross-vehicle access.
- Safe behavior during projection updates.

## Final Decision

QR access must use secure, revocable credentials
and expose only authorized vehicle projections.

This decision is accepted.
