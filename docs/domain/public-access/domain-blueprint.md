# Public Access - QR and Public Projection Domain Blueprint

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Mission
Serve approved public views of a particular vehicle through unpredictable, scoped, revocable bearer credentials, without exposing private aggregates. ADR-0011 is **Accepted**.

## Candidate model
- `PublicAccessGrant` Aggregate Root: id, vehicleId, authorized scope, status, createdAt, expiresAt, policy version, hashed token verifier.
- `PublicVehicleProjection`: sanitized display fields and source freshness metadata, never a Vehicle aggregate.
- `PublicFieldPolicy`: explicitly authorized fields; VIN/plate require opt-in and legal/security review.
- `GrantAudit`: issuance, rotation, revocation, access metadata (minimized, retained per policy).

## Proposed operations
IssueGrant, RotateGrant, RevokeGrant, UpdateFieldScope, ResolvePublicVehicle. All management operations require resource-level backend authorization; viewing requires valid scoped credential. A public token cannot authorize mutation.

## Security rules
Use cryptographically secure randomness for token; store a cryptographic digest/verifier rather than raw token. Do not derive token from VIN, vehicle ID or plate. Enforce TLS, rate limiting, safe cache/referrer policies, careful logs and no embedded third-party tracker leaks. A copied QR is effectively a copied bearer credential: design the projection for that exposure.

## Information policy
Candidate default is make/model/type + minimal maintenance summary with redaction. Full VIN, exact plate, names, documents, owner contact and financial details are **off by default**. Any field opt-in must be checked at issuance and access time.

## Consistency
Projections from Vehicle Registry and Maintenance may be eventually consistent. Revocation must be enforced authoritatively at token validation time, even if caches lag. Ownership/access changes require grant reevaluation rather than assuming existing grants stay valid.

## Tests
Invalid/revoked/expired token; rotation invalidates old token; no ID enumeration; forbidden fields absent; cache after revocation; simultaneous rotate/revoke; token secret never logged; cross-vehicle leakage; customer data deletion; stale projected maintenance data.

## Open decisions
Grant lifetime/default scope, policy on ownership transfer, QR URL shape, expiry, read audit retention, QR presentation, projection freshness SLA, issuing permissions.
