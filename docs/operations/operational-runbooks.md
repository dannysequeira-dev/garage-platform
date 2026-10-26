# Operational Runbooks - Incident and Reconciliation Procedures

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Runbook RB-01: Outbox publication delayed
**Signal:** oldest pending outbox age exceeds approved threshold. **Inspect:** producer DB transactions, relay process, Kafka permissions, schema compatibility. **Mitigate:** restore relay, bounded retry; do not manually publish a malformed payload to production. **Verify:** eventId and logical source dedup, consumer lag drains, no duplicate effect. **Close:** incident reason and replay audit.

## RB-02: Consumer poison event
**Signal:** repeated permanent validation errors/DLQ. **Inspect:** event type/version, source ID, privacy-sanitized payload, contract deployment mismatch. **Mitigate:** quarantine; approve schema migration or corrected event. **Verify:** no silent event loss, no unbounded retries, downstream business reconciliation.

## RB-03: Maintenance completed but odometer not updated
**Signal:** MaintenanceCompleted outbox committed but Registry lacks matching source observation. **Inspect:** Kafka lag, Inbox, source revision, vehicle existence, quarantine reason. **Mitigate:** safe replay/reconciliation; never update Registry tables directly from Maintenance. **Verify:** exactly one logical observation, no current reading regression. **Reference:** ADR-0016.

## RB-04: Stuck booking Saga
**Signal:** pending step exceeds deadline or compensation fails. **Inspect:** Saga persisted state, command IDs, inventory reservation, appointment ID and late responses. **Mitigate:** retry idempotently, issue valid compensation or manual resolution. **Verify:** no orphaned stock/appointment and correct customer-visible status. Do not roll back physical work magically.

## RB-05: Stock discrepancy
**Signal:** negative available, unexpected movement or reserved count inconsistency. **Inspect:** ledger, affected stock bucket, command dedup and concurrency conflicts. **Mitigate:** freeze affected operations, perform approved auditable adjustment. **Verify:** onHand/reserved ledger and physical count reconciliation.

## RB-06: QR token exposure
**Signal:** suspected public token in logs or suspicious access. **Inspect:** impacted grant IDs without broadcasting raw tokens. **Mitigate:** revoke/rotate, scrub secrets from telemetry, inspect referrer/caching leaks. **Verify:** old token denied, no unauthorized private projection; communicate that already-copied visible data cannot be retracted.

## RB-07: Cross-tenant access incident
**Signal:** foreign org data access or authorization policy bypass. **Immediate:** restrict affected endpoint, preserve evidence, revoke relevant grants/sessions, coordinate privacy/legal response. **Verify:** all affected resource/record types, audit scope, regression tests. Do not assume a frontend fix secures backend authorization.

## Global runbook metadata to approve
Service owner, on-call, severity, escalation, dashboard, audit references, change approval, incident communications and closure criteria. No live operational access is implied by this document.
