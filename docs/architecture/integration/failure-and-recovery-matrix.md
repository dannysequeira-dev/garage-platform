# Distributed Failure and Recovery Matrix

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Failure ownership
| Failure | Expected safe behavior | Detection | Recovery test |
| --- | --- | --- | --- |
| DB commit fails before Outbox written | Rollback aggregate and Outbox | Transaction error | Fault-injected rollback |
| DB commits, process crashes before Kafka | Outbox pending; relay publishes later | Outbox age | Kill producer after commit |
| Kafka publishes duplicate event | Consumer Inbox + source dedup avoids duplicate effect | Duplicate metric | Replay same event |
| Consumer crashes after DB commit before acknowledgment | Re-delivery becomes no-op | Consumer restart trace | Kill at commit boundary |
| Invalid message schema | Reject/quarantine safely; no silent drop | DLQ + alert | Unsupported schema |
| Stale odometer event arrives last | Record valid history, no blind regression | Observation warnings | Out-of-order integration test |
| Missing Vehicle reference | Configured reconcile/retry or durable review | Pending-event queue | Event before registration |
| Reservation succeeded, appointment failed | Saga compensates reservation | Saga state | Failure + compensation |
| Compensation fails | Durable `REQUIRES_INTERVENTION` | Stuck Saga alert | Fail release adapter |
| Late success after timeout | Correlate idempotently and reconcile | Late response monitor | Delayed event |
| Stock reservation race | Only capacity-safe reservation commits | Conflict metric | Parallel reserve test |
| Same slot booked simultaneously | Capacity invariant holds | Booking conflict | Parallel booking test |
| QR revoked while projection cached | Validation denies old token | Access audit | Cache/revoke test |
| Notification provider timeout after possible send | Provider-aware dedup; no blind infinite retry | Unknown outcome alert | Timeout replay |
| Partial tenant authorization config | Deny by default, audit | Security event | Cross-tenant denial test |
| Backup corruption | Restore to isolated environment and verify | Recovery drill | Restore exercise |

## Classification
- **Transient:** retry with bounded backoff and jitter, preserve operation identity.
- **Business rejection:** durable terminal outcome or explicit review; do not hammer another service.
- **Contract poison:** quarantine with contract/version evidence, never expose private payload broadly.
- **Compensation failure:** persist and alert; operator decision with audit, not silent data correction.

## Reconciliation
Define periodic checks for outbox stuck age, lagging consumer, orphaned stock reservations, appointments without confirmed orders, pending Saga > deadline, odometer source mismatch, unreleased QR after ownership policy changes and notification unknown outcomes.

## Observability fields
`service`, `traceId`, `correlationId`, `operationId`, `sagaId` (when relevant), `eventType`, `eventId`, `schemaVersion`, `errorCode`; never log raw QR token, auth secrets, or unapproved PII. Prometheus labels must be low-cardinality; identifiers belong in structured logs/traces, not metric labels.
