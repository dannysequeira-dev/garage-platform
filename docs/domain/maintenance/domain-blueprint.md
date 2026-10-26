# Maintenance - Tactical Domain Blueprint

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Mission
Record performed services with traceable provenance, manage service orders and preventive plans, and publish `MaintenanceCompleted` including a source-attributed odometer observation. Maintenance does **not** own authoritative odometer history.

## Candidate domain concepts
| Concept | Candidate role | Core rules |
| --- | --- | --- |
| MaintenanceOrder | Aggregate Root | Workflow/status, requested work, authorization, eventual completion |
| MaintenanceRecord | Immutable historical fact or aggregate | Completed work, performedAt, odometer observation, source trust |
| MaintenancePlan | Aggregate Root | Calendar and/or distance schedule, enabled state, last service basis |
| MaintenanceDue | Derived state/projection | Evaluated from approved plan, current facts and time |
| MaintenanceLine | Entity/VO within order | Service code, description, labor or parts reference |
| ServiceOrigin | Value Object | `OWNER_REPORTED`, `WORKSHOP_RECORDED`, `VERIFIED` only after policy |
| ServiceCost | Value Object | Money + explicit currency/rounding |

## Proposed workflows
1. Owner self-reported completion: create historical record without inventing service order, preserve origin and attachments.
2. Workshop order: draft/authorize/accept work; reserve stock/appointment if applicable; record actual work; complete; no reopening completed fact without correction event/record.
3. Maintenance plan: anchor and interval(s) -> calculate due from authoritative or confidence-ranked reading; produce deduped due changes.

## Completion transaction
Validate permissions and completion preconditions, create record, close order if applicable, persist both under local ACID transaction, record a **versioned** `MaintenanceCompleted` integration message via Outbox. Include `vehicleId`, `maintenanceId`, `completedAt`, `odometer {value, unit, observedAt}`. Avoid direct synchronous Registry DB write; Registry decides acceptance.

## Business questions blocking final aggregates
- Can work be partially completed and still recorded as a separate completion?
- Does order closure require stock-consumed confirmation, or is part usage optional?
- Are prices, estimates and approvals in V1?
- What is a correction vs deletion of historical record?
- Should self-reported history be shared across organizations and under what trust label?
- Can a maintenance plan be tied to a service category, vehicle, component or template?
- Which date/distance trigger takes priority, and how are unknown readings handled?

## Events and errors
Candidate events: `MaintenanceOrderCreated`, `MaintenanceCompleted` (accepted integration), `MaintenanceDueDetected`, `MaintenancePlanChanged`, `MaintenanceRecordCorrected`. Errors: not authorized, invalid observed date, duplicate completion, incompatible order state, unapproved change, missing external outcome.

## Acceptance exercises
Caleb's owner-reported oil/filter service at ~4,700 KM; historical service entered after newer reading; duplicated completion command; cancelled order with reserved stock; owner with no workshop; partially performed service and correction of incorrect mileage. Each must preserve provenance and idempotency.
