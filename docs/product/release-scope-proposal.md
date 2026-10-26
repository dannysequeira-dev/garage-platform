# Proposed V1 Release Scope and Phasing

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Proposed product positioning
A vehicle lifecycle platform for private owners and authorized workshops; it is **not** a full ERP, marketplace, insurer, or telematics platform. This document structures the existing Draft vision into an approvable baseline.

## Proposed release slices
| Slice | Outcomes | Dependencies | Candidate status |
| --- | --- | --- | --- |
| V1-A: personal records | Authenticated user, vehicle registration, ownership assertions, odometer history, owner-recorded maintenance | Identity, Vehicle, Maintenance | V1 candidate |
| V1-B: prevention | Time/distance plans, due projections, reminders | V1-A, Notifications | V1 candidate |
| V1-C: workshop | Organization membership, service orders, appointments, stock ledger/reservations, service completion | Security, Inventory, Scheduling, Saga decisions | V1 candidate |
| V1-D: controlled sharing | Vehicle-scoped revocable QR, field-level public projection, access audit | V1-A, Public Access/privacy policy | V1 candidate |

## Proposed boundaries
**In candidate V1:** motorcycles and passenger vehicles; one authenticated user may manage multiple vehicles; workshops see only authorized records; self-reported records retain their provenance; stock is authoritative in Inventory; no global direct DB joins; QR reveals minimal data.

**Deferred by default:** payments, e-commerce checkout, full electronic invoicing, ERP, predictive AI, continuous GPS/OBD, complex fleet dispatch, sophisticated supplier procurement, direct consumer IoT integrations.

**Needs explicit approval:** multi-location stock; mobile and desktop apps in first release; full workshop financial estimates; regulatory compliance for Costa Rica or other jurisdictions; warranty and vehicle sale workflows.

## Scope checkpoints
- PS-001: choose individual-first, workshop-first or combined release.
- PS-002: vehicle classes and jurisdiction.
- PS-003: workshop order depth (simple, approvals, estimates, partial completion).
- PS-004: inventory and location depth.
- PS-005: capacity model for appointments.
- PS-006/007: owner verification and maintenance source trust.
- PS-008/009: channels and attachments.
- PS-010/011: organization isolation and cross-workshop sharing.
- PS-012: QR visible fields.

## Acceptance before ratification
Record which slices are truly V1; approve user-facing outcomes for each; confirm data-sharing authorization and business rules; attach measurable success criteria and nonfunctional targets; update Draft vision and related ADRs. No code is authorized by this proposed release plan.
