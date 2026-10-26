# ADR-0012: Observability and Infrastructure Monitoring

- **Status:** Draft
- **Date:** 2026-10-26
- **Scope:** Telemetry, metrics, logs, tracing, and monitoring

## Context

Garage Platform contains distributed Java services,
Kafka consumers, local transactions, outbox relays,
and long-running Saga workflows.

Operational failures may span multiple services
and asynchronous operations.

The platform requires observable business
and infrastructure behavior.

Several candidate tools have been identified,
but the final deployment architecture and
operational cost have not been approved.

## Proposed Direction

### OpenTelemetry

Use OpenTelemetry as the preferred
instrumentation standard.

It may provide:

- Distributed traces.
- Context propagation.
- Standardized telemetry instrumentation.
- Metrics export.
- Structured log correlation.

OpenTelemetry is not itself the primary
persistent telemetry database.

An OpenTelemetry Collector may be introduced
for centralized collection and routing.

### Prometheus

Prometheus is the preferred metrics
storage and query component.

Candidate metrics include:

- HTTP request latency.
- HTTP error rate.
- JVM heap and garbage collection.
- Kafka consumer lag.
- Outbox delivery delay.
- Saga duration and failures.
- Inventory reservation failures.

Metrics must avoid high-cardinality labels
containing per-request or per-aggregate IDs.

### Grafana

Grafana may provide:

- Operational dashboards.
- Metric exploration.
- Alerting.
- Service health views.

The exact dashboard and alerting design
remains to be defined.

### Elasticsearch and Kibana

Elasticsearch and Kibana are candidates
for centralized log storage and analysis.

Logs should be structured and correlated
with distributed tracing metadata.

Potential fields include:

- Service name.
- Operation.
- Trace identifier.
- Correlation identifier.
- Saga identifier.
- Error classification.

Sensitive personal data and credentials
must not be logged.

### Zabbix

Zabbix is a candidate for monitoring:

- Virtual or physical hosts.
- Disk availability.
- Network availability.
- Hardware resources.
- External infrastructure checks.
- SNMP-enabled devices where relevant.

Zabbix must have an operational responsibility
distinct from application telemetry.

Duplicating all Prometheus monitoring in
Zabbix is not a requirement.

### Distributed Correlation

Tracing and logs should propagate
the relevant correlation context across
HTTP and Kafka operations.

Asynchronous workflows must preserve
business correlation identifiers independently
of trace context.

A Saga identifier is not interchangeable
with a distributed trace identifier.

### Business Observability

Technical metrics alone are insufficient.

The platform should observe:

- Pending maintenance orders.
- Failed reservations.
- Uncompleted Sagas.
- Delayed events.
- Failed notification deliveries.
- Inventory inconsistencies.

### Reliability

Services should define measurable
availability and reliability objectives
when operational requirements are known.

Alerts must be actionable.

Excessive alerts without clear ownership
should be avoided.

### Data Retention

Telemetry retention, sampling, indexing,
and archival require an explicit cost policy.

The platform must not store sensitive data
indefinitely merely for diagnostic convenience.

## Alternatives to Evaluate

- Elasticsearch/Kibana versus Loki-based logging.
- Elastic tracing versus Tempo or Jaeger.
- Zabbix versus existing infrastructure telemetry.
- Direct exporters versus an OTel Collector.
- Self-hosted versus managed observability.

## Open Decisions

- Final telemetry storage backends.
- Log retention and cost budget.
- Trace sampling policy.
- Alerting ownership.
- Deployment topology.
- Need for Zabbix in the initial environment.

## Acceptance Criteria

A final decision must document:

- Responsibility for each telemetry signal.
- Storage and retention.
- Correlation strategy.
- Required service metrics.
- Operational dashboards.
- Alerting and ownership.
- Infrastructure cost.

## Status

Draft.

OpenTelemetry, Prometheus, Grafana,
Elasticsearch/Kibana, and Zabbix remain
candidate components with distinct responsibilities.

This ADR does not mandate deploying
every candidate tool.
