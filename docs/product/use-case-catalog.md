# V1 Candidate Use-Case Catalog

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Status
Catalog IDs are stable design references; **V1 inclusion is proposed, not ratified**. The detailed domain blueprints contain specific invariants and state transitions. Every entry must later acquire executable acceptance tests.

| ID | Context | Use case | Actor | Key postcondition | Critical alternative |
| --- | --- | --- | --- | --- | --- |
| UC-VR-01 | Vehicle | Register vehicle | Caretaker | Immutable VehicleId created | Duplicate/invalid identification |
| UC-VR-02 | Vehicle | Correct vehicle identity | Authorized manager | Audit-preserving correction | Invalid VIN policy |
| UC-VR-03 | Vehicle | Establish/transfer ownership | Authorized actor | Temporal relations changed atomically | Conflicting transfer |
| UC-VR-04 | Vehicle | Record manual odometer | Authorized actor | Source-attributed observation | Anomaly/replacement |
| UC-VR-05 | Vehicle | Consume maintenance odometer | Internal consumer | Idempotent history update | Duplicate/out-of-order |
| UC-MNT-01 | Maintenance | Open service order | Service advisor | Pending order | Insufficient grants |
| UC-MNT-02 | Maintenance | Record owner maintenance | Caretaker | Completed service provenance | Invalid mileage/date |
| UC-MNT-03 | Maintenance | Complete workshop work | Technician/advisor | Completed service and Outbox | Stock/approval conflict |
| UC-MNT-04 | Maintenance | Manage preventive plans/due | Caretaker | Due projection | Missing reading |
| UC-INV-01 | Inventory | Register part | Manager | Scoped catalog entry | Duplicate SKU |
| UC-INV-02 | Inventory | Receive/adjust stock | Stock manager | Audited ledger movement | Negative available stock |
| UC-INV-03 | Inventory | Reserve/release stock | Maintenance workflow | Durable reservation | Concurrent depletion |
| UC-INV-04 | Inventory | Consume reserved stock | Service workflow | Single committed consumption | Duplicate completion |
| UC-SCH-01 | Scheduling | Request/confirm appointment | Service advisor | Resource-safe appointment | Slot unavailable |
| UC-SCH-02 | Scheduling | Reschedule/cancel | Authorized user | Audit-preserving transition | Concurrency race |
| UC-PUB-01 | Public | Issue public QR grant | Authorized manager | Revocable token | Field policy violation |
| UC-PUB-02 | Public | View/revoke QR | Visitor/manager | Only approved projection | Expired/revoked grant |
| UC-NTF-01 | Notifications | Deliver reminder | Internal event | Deduplicated delivery attempt | Opt-out/provider failure |
| UC-IAM-01 | Identity | Sign in | Account holder | Authenticated principal | Invalid session |
| UC-IAM-02 | Organizations | Manage membership | Org admin | Membership updated | Privilege escalation |
| UC-IAM-03 | Access | Grant/revoke vehicle access | Authorized actor | Audited resource grant | Unauthorized grant |
| UC-IAM-04 | Access | Approve selective history sharing | Authorized actor | Scoped approved disclosure | Cross-workshop denial |

## Example detailed acceptance case: UC-MNT-02
**Given** an authenticated caretaker with permission to add a self-reported maintenance entry for Vehicle V, **when** they record a completed service at valid observed mileage and time, **then** Maintenance persists the record and an outbox message atomically, visibly labels the record as self-reported, and eventual Registry propagation does not block completion. **If** event delivery repeats, Registry must not duplicate the odometer observation. **If** Registry later flags the observation, Maintenance must not silently delete the completed fact.

## Example detailed acceptance case: UC-VR-03
**Given** a vehicle with owners A, B and C, **when** an authorized actor transfers B's relationship to D, **then** B's period closes, D's begins, and A and C stay unchanged. If persistence fails halfway or two transfers conflict, none of the attempted partial changes becomes an accepted transfer.

## Example detailed acceptance case: UC-INV-03
**Given** 3 available units in one stock consistency boundary, **when** two independent requests reserve 2 units concurrently, **then** at most one succeeds unless stock is replenished in between. Retried reservation commands must not create extra holds.

## Required next pass
For **every** V1-selected use case: document field-level validation, states, pre/postconditions, authorization, idempotency key, errors, concurrency, audit, UX messages, API/event contracts and test scenarios. The current table is the backbone, not a replacement for full domain specifications.
