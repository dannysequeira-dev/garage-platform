# HTTP API Catalog - Proposed V1 Resources

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Position
These are **candidate external routes** and not approved API contracts. Authoritative resource authorization is performed in the owning backend. Gateway/ingress placement remains OD-016; HTTP transport concerns do not enter Domain Core.

| Capability | Candidate method and route | Scope | Key semantics |
| --- | --- | --- | --- |
| Vehicle registration | `POST /v1/vehicles` | `vehicle:create` | Idempotent request key, immutable VehicleId |
| Vehicle summary | `GET /v1/vehicles/{vehicleId}` | `vehicle:read` | Authorized fields, ETag candidate |
| Vehicle identity correction | `PATCH /v1/vehicles/{vehicleId}/identification` | `vehicle:edit` | Concurrency version and audit |
| Ownership relationships | `GET /v1/vehicles/{vehicleId}/ownerships` | `ownership:read` | Authorized temporal records only |
| Add co-owner | `POST /v1/vehicles/{vehicleId}/ownerships` | `ownership:manage` | No blanket overwrite |
| Transfer relation | `POST /v1/ownerships/{ownershipId}/transfer` | `ownership:manage` | Atomic, idempotent, versioned |
| Odometer observations | `GET /v1/vehicles/{vehicleId}/odometer-observations` | `vehicle:read` | Paginated history |
| Manual reading | `POST /v1/vehicles/{vehicleId}/odometer-observations` | `vehicle:edit` | ObservedAt, source, idempotent |
| Maintenance history | `GET /v1/vehicles/{vehicleId}/maintenance` | `maintenance:read` | Explicit sharing policy |
| Owner maintenance | `POST /v1/vehicles/{vehicleId}/maintenance-records` | `maintenance:create` | Provenance preserved |
| Service order | `POST /v1/maintenance-orders` | `maintenance:create` | Workflow ID |
| Complete order | `POST /v1/maintenance-orders/{id}/complete` | `maintenance:complete` | One logical completion |
| Maintenance plan | `POST /v1/vehicles/{vehicleId}/maintenance-plans` | `maintenance:plan:manage` | Date/km rule |
| Stock list | `GET /v1/inventory/stock` | `inventory:read` | Org/location scope |
| Stock reservation | `POST /v1/inventory/reservations` | `inventory:reserve` | Idempotent command |
| Appointment request | `POST /v1/appointments` | `schedule:manage` | Resource capacity |
| QR grant create | `POST /v1/vehicles/{vehicleId}/public-grants` | `qr:issue` | Return secret once |
| QR grant revoke | `POST /v1/public-grants/{grantId}/revoke` | `qr:revoke` | Immediate invalidity |
| Public view | `GET /public/v/{publicId}` plus bearer credential mechanism | QR grant | Only permitted projection |
| Organization member | `POST /v1/organizations/{id}/members` | `org:member:manage` | Membership policy |

## HTTP behaviors
- Use standard status semantics: 201 created; 200/204 success; 400 malformed request; 401 unauthenticated; 403 forbidden; 404 absent or concealed; 409 version/idempotency conflict; 422 valid syntax but failed domain validation when selected; 429 throttled.
- Use Problem Details (RFC 9457) with stable application error codes and correlation IDs; never leak internal stack traces.
- Support cursor pagination for growing histories; explicit stable sort by effective date/id tie-break.
- Idempotency keys for externally retried state-changing operations. Conflict if key reused with different payload; scope keys by principal/operation.
- Use `If-Match` or explicit version precondition for conflict-prone updates if approved; no hidden last-write-wins.
- Read consistency: communicate eventual update after `MaintenanceCompleted` instead of promising instantaneous Registry view.

## Important boundaries
The frontend does not call Kafka. Generated TypeScript clients live behind HTTP adapters and must not become frontend domain models. Public QR request should avoid token in unsafe analytics/referrer/log contexts; exact token conveyance must be selected with security review.

## Approval needed
Route naming/versioning, resource representation, relation vs command endpoints, BFF, auth flows, paginated data, filtering, caching, and error code catalog must be ratified with OpenAPI tests.
