# Authorization Architecture and Resource Access Model

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Non-negotiable distinctions
Authentication determines **who** the caller is. Authorization determines **whether** that principal can perform an operation on **that** resource within a specific organization and legal/privacy context. Vehicle owner Party, User account, Organization membership and QR grant are not interchangeable.

## Candidate policy decision
`authorize(principal, action, resource, context) -> Allow/Deny + reasonCode + policyVersion`; input includes authenticated identity, current membership, resource grants, vehicle relationship, record provenance, cross-organization sharing policy, and exceptions. Decisions happen on the server even if UI hides a button.

## Proposal: permission grants
- Global support operators receive explicitly scoped, audited operational permissions, not all customer records by default.
- Organization membership grants **candidate** roles for workshop operations but not universal vehicle/history access.
- Vehicle access grant captures `principal`, `resource VehicleId`, operation scopes, grantor/reason, effective/expiry/revocation.
- Legal Party ownership is a fact that may be used in policy, not automatic authenticated entitlement.
- Public QR token is a bearer capability granting only its sanitized projection, no mutation rights.

## Policy enforcement points
1. Authentication at gateway/app ingress where applicable.
2. Resource-level authorization in owning backend use case.
3. Projection/read filtering and field-level disclosure checks.
4. Persistence scoping (tenant isolation) as defense in depth, not substitute for business rules.
5. Short-lived media signed URL issuance after document-specific authorization.
6. Authorization reevaluation for owner transfer, grant revocation, membership change and QR policy changes.

## Cross-workshop records
Work created by Workshop A is not automatically readable by Workshop B. Distinguish private workshop notes, customer-shareable service summary and publicly authorized QR summary. A VehicleId match or VIN match must not bypass disclosure policy. Proposed explicit selective sharing grant with scope, record types, recipients and time limit.

## Decision examples
| Request | Proposed result | Why |
| --- | --- | --- |
| Owner caretaker reads own granted vehicle summary | Allow after current grant | Resource authorization |
| Workshop B asks for Workshop A internal cost breakdown | Deny by default | Cross-org confidentiality |
| Public visitor reads exact VIN not in QR grant | Deny/omit | Field scope |
| Revoked team member adjusts stock | Deny | Current membership |
| Co-owner recorded only as Party but has no linked login | No session access inferred | Party != User |
| Platform support with no break-glass authorization | Deny private history | Least privilege |

## Audit
Record sensitive allows/denies, grant issuance/revocation, ownership changes, QR field modifications, inventory adjustments and emergency support access with low-PII structured metadata.

## Approval blockers
Identity provider (OIDC), subject binding Party↔User, membership storage, tenant policy, consent, privileged support process, resource ownership representation and legal obligations. ADR-0010 is still Draft.
