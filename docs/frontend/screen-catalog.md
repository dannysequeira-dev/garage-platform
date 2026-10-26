# Web Screen Catalog - Candidate V1

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Catalog
Screens are proposals; each must pass authorization, accessibility, and no-data/error design. No UI library is selected by this catalog.

| Screen ID | Screen | Main actor | Data/actions | Critical state |
| --- | --- | --- | --- | --- |
| UI-01 | Sign-in | Account holder | OIDC integration and account state | Expired session |
| UI-02 | My Garage | Caretaker | Vehicle list, due summary | Empty garage, no grant |
| UI-03 | Vehicle details | Authorized viewer | Identity, odometer, history | Stale Registry projection |
| UI-04 | Register vehicle | Caretaker | Descriptors, VIN/plate options | Duplicate identifier conflict |
| UI-05 | Ownership timeline | Authorized viewer | Temporal owners and provenance | Multiple active owners |
| UI-06 | Transfer ownership | Authorized manager | One relation transfer | Version conflict |
| UI-07 | Maintenance timeline | Authorized viewer | Service records, source labels | Private workshop records |
| UI-08 | Record maintenance | Caretaker/advisor | Time, mileage, parts, notes | Duplicate command |
| UI-09 | Maintenance plan | Caretaker | Date/km intervals, due status | Missing odometer |
| UI-10 | Workshop orders | Advisor/manager | Order queue/status | Saga pending |
| UI-11 | Workshop order detail | Assigned staff | Work, parts, completion | Partial failure |
| UI-12 | Calendar/appointments | Advisor | Availability and booking | Slot race |
| UI-13 | Inventory catalog | Workshop staff | Parts and filtered stock | Zero stock |
| UI-14 | Inventory reservation | Authorized staff | Reserve/consume/release | Conflict/expiration |
| UI-15 | QR settings | Authorized manager | Preview, issue, revoke, rotate | Secret once-only |
| UI-16 | Public QR page | QR visitor | Minimal approved vehicle projection | Invalid/revoked token |
| UI-17 | Notifications/preferences | Account holder | Due messages and opt-out | Delivery uncertain |
| UI-18 | Org/team administration | Org admin | Roles, memberships | Privilege escalation risk |
| UI-19 | Privacy and sharing | Authorized principal | Vehicle grants and access audit | Cross-org restriction |
| UI-20 | Operations/reconciliation | Support admin | Stuck Saga/outbox and error codes | High-trust access |

## Per-screen specification template
For each included screen record: user goal, entry route, resource owner, permission predicate, input fields, transport adapter, success state, empty state, loading, validation, network failure, unauthorized, concurrency conflict, responsive layout, keyboard/focus, privacy classification, localization and analytics restrictions.

## Screen acceptance sample: UI-08
Service entry shows vehicle identity, effective date, observed mileage with unit, provenance (self-reported/workshop) and clear completion action. Submission uses stable operation identity; duplicate taps cannot create two historical facts; success remains visible even while Registry odometer updates eventually.

## Screen acceptance sample: UI-16
Public page must contain no private cost, owner name, VIN or plate unless specifically granted and revalidated. No authenticated dashboard controls; invalid token and revoked token receive safe generic responses.
