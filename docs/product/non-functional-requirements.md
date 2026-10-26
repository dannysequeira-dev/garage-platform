# Non-Functional Requirements and Quality Attributes

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Scope
These requirements are **design targets requiring approval**. Do not report example numbers as committed SLOs. Measure separately for low-cost hobby deployment and commercial V1 operations.

| NFR | Attribute | Proposed requirement | Measurement / test | Decision needed |
| --- | --- | --- | --- | --- |
| NFR-01 | Availability | Publish service and platform SLOs | Synthetic checks, incident budget | OD-023 |
| NFR-02 | API latency | Define p50/p95/p99 by key journey; protect tail latency | Traces/load tests | Traffic profile |
| NFR-03 | Consistency | Local invariants ACID; cross-context propagation eventual | Fault-injection integration tests | Event lag tolerance |
| NFR-04 | Delivery | Reliable Outbox + consumer dedup for relevant effects | Crash/replay tests | Relay/retention |
| NFR-05 | Audit | Immutable attributable records for ownership, QR, stock and financial changes | Traceability tests | Retention policy |
| NFR-06 | Security | Server-authorized resource access, least privilege, token rotation | Threat-driven tests | Identity and tenancy |
| NFR-07 | Privacy | PII minimization; no private QR defaults; scoped access | Access matrix and penetration review | Applicable jurisdiction |
| NFR-08 | Portability | TS Core/Application tests without React/Next | Dependency tests | Tooling choices |
| NFR-09 | Accessibility | Keyboard, semantic controls, readable errors | Manual/a11y automated tests | Target standard |
| NFR-10 | Recovery | Defined RPO/RTO, restore verification | Restore drills | OD-023 |
| NFR-11 | Observability | Correlation over HTTP/Kafka/Saga, alert owners | Trace/log search and incident drills | OD-022 |
| NFR-12 | Cost | Monthly infrastructure and telemetry budgets | Forecast vs actual | Commercial assumptions |
| NFR-13 | Localization | Dates, currencies, distance and language aware | Intl tests | OD-026 |
| NFR-14 | Integrity | Prevent negative available stock and duplicate logical readings | Concurrency tests | Domain choices |
| NFR-15 | Build reproducibility | Independently build, test, version and deploy service artifacts | CI rebuild comparison | Tool versions |
| NFR-16 | Contract stability | Backward-compatible evolution or documented version migration | Schema diff and consumer tests | OD-027 |

## Data integrity
- Monetary calculations use precise decimals and explicit currency; no silent FX or rounding.
- A `VehicleId` stays stable across plate changes, owners, and maintenance records.
- VIN, plate and ownership data require jurisdiction-aware validation rather than arbitrary global uniqueness assumptions.
- Event processing must resist duplicate and out-of-order delivery.
- No single global ACID transaction spans microservices.

## Service level template (not approved)
Document per journey: expected requests per second, p95 response bound, availability target, acceptable consumer lag, queue depth, recovery steps, load test dataset and resource budget. Avoid choosing values before estimating V1 usage and operational cost.

## Security/privacy acceptance
Test explicit denied access on foreign vehicle, expired vehicle grant, cross-organization workshop record, revoked QR, tampered object attachment URL and role escalation. Ensure trace context does not leak PII or bearer secrets.

## Performance and scale exercise
Include small initial deployment and a future growth scenario; identify which contexts require pagination, indexing, background processing and precomputed projections. Verify that a large vehicle history does not require loading unbounded child collections in one aggregate.

## Open decisions
Traffic estimates, user geography, legal retention, p95/SLO thresholds, downtime, RTO/RPO, language options, multi-region policy and attachment sizes are not approved.
