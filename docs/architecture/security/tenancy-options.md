# Multi-Tenancy and Global Vehicle Identity - Decision Brief

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Question
Garage Platform spans private owners and workshops. A vehicle can have multiple Party owners and service history originating in different organizations. Should identity be globally stable, how is data stored, and who can see it?

## Options for identity
| Option | Benefits | Risks |
| --- | --- | --- |
| A: Global VehicleId + scoped access grants | Continuous identity/odometer history across owners; less duplication | Complex privacy/governance, authorization correctness critical |
| B: Tenant-scoped vehicle records | Simpler tenant isolation | Duplicate identities, fragmented history, difficult transfer |
| C: Global directory + tenant-specific operational records | Separate identity from confidential work | More service boundaries, projection/reconciliation complexity |

**Recommendation for evaluation:** option C or A with strict resource grants, but do not ratify until legal, security and UX scenarios are resolved.

## Options for persistence
| Strategy | Strength | Cost/complexity |
| --- | --- | --- |
| Shared tables tenant_id | Efficient for many small tenants | Highest query policy rigor; RLS optional defense |
| Schema per tenant | Schema isolation | Migration/connection overhead |
| Database per tenant | Stronger isolation | Cost and operations overhead |
| Hybrid | Can isolate high-risk tenants | Complex routing and portability |

## Required cross-organization scenarios
- One owner has garage account and authorizes Workshop A for a service only.
- Workshop B enters same VIN without owner's consent; must not see hidden identity or A's notes.
- A vehicle changes owner; new owner sees permitted vehicle-level history, not all legally restricted historical documents.
- Workshop A changes ownership of its business account.
- A vehicle has co-owners from different organizations.
- Public QR owner revokes a grant while a read cache exists.
- A tenant leaves platform and requests export/deletion within applicable legal requirements.

## Decision deliverables
Select (1) VehicleId scope, (2) Party system of record, (3) tenant persistence, (4) identity binding, (5) record provenance ownership, (6) resource grant model, (7) migration/portability, (8) legal retention; then amend ADR-0010 and ADR-0015 through proper governance. No option above is already accepted.
