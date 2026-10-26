# AsyncAPI - Proposed Integration Schemas

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
Versioned inter-context messaging contract **proposals**, not operational Kafka topic configuration. They illustrate the expected separation between internal Domain Events and public Integration Events.

## Included
`garage-platform-events.v1.proposed.yaml` covers selected message envelopes and the accepted semantic requirement that `MaintenanceCompleted` includes source odometer observation. Schema names, serialization format, Kafka topic names, and registry are not final.

## Design constraints
- Integration messages carry immutable facts with unique event ID and appropriate business correlation.
- Producer is responsible for versioning and data minimization.
- Eventual delivery and duplicate handling are expected.
- Consumer maps contracts through its ACL to local use case commands.
- `MaintenanceCompleted` maps to Registry odometer history, not a direct cross-service DB write.
- Contract approval requires explicit producer/consumer compatibility tests, partition choices, failure handling and privacy review.
