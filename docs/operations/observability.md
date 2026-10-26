# Observability Blueprint and Signal Ownership

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Direction
Instrument services with OpenTelemetry-compatible context propagation. Prometheus, Grafana, Elasticsearch/Kibana and Zabbix are *candidates with distinct roles*, not an instruction to deploy all simultaneously (ADR-0012 Draft).

## Signal catalog
| Signal | Examples | Design constraints |
| --- | --- | --- |
| Metrics | HTTP request rate/error/latency, DB pool, JVM, Kafka lag, Outbox age, Saga stuck count | No vehicleId/userId labels |
| Traces | HTTP request -> service -> DB -> outbox; consumer -> domain action | Async correlation, sampling policy |
| Logs | Structured errors, domain error code, operation and trace ID | Redact PII, secrets, QR tokens |
| Business metrics | Overdue plans, reservation failures, uncompleted work, delivery failure rate | Reconcile to authoritative sources |
| Infrastructure | Disk, memory, network, backups, broker health | Role for Zabbix vs Prometheus explicit |

## Event propagation context
Every Integration Event should carry business `correlationId` and optional `causationId`; trace context propagation is distinct. Kafka span linkage and Saga ID may continue a business process across multiple traces.

## Required dashboards
- Service golden signals: volume, errors, latency, saturation.
- Kafka: producer errors, consumer lag, poison topics and replays.
- Outbox/Inbox: oldest unpublished, duplicate count, processing failure.
- Saga: active count, stuck duration, failed compensation.
- Domain integrity: stalled odometer propagation, stock adjustment anomalies, appointment conflict rate.
- Security: denied QR bursts, cross-org access denials, credential rotation anomalies.

## Alerting and ownership
Every alert must have severity, threshold based on SLO, on-call/owner, escalation path and runbook. Avoid alert storms. Business counters must be privacy-conscious and low-cardinality. Exact thresholds and retention await approved budget and usage assumptions.

## Tool evaluation
| Component | Proposed responsibility | Alternative evaluation |
| --- | --- | --- |
| OTel SDK/Collector | Vendor-neutral instrumentation/transport | Direct exporters where justified |
| Prometheus | Metrics store | Managed metrics |
| Grafana | Dashboards/alert UI | Managed observability |
| Elasticsearch + Kibana | Searchable structured logs | Loki or managed logs |
| Tempo/Jaeger/Elastic | Trace backend | Compare cost and querying |
| Zabbix | Host/network/infra monitoring | Existing Prometheus infra exporters |

## Review gate
Approve backend choices, telemetry budget, retention, SLOs, label taxonomy, security controls, deployment plan and incident ownership; then update ADR-0012.
