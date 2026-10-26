# End-to-End User Journeys

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Journey format
Each journey lists a trigger, minimum success path, visible uncertainty, and failure outcomes. These do not prescribe final API or UI routes.

### J-01: Register a private vehicle
**Actor:** authorized caretaker. **Trigger:** newly acquired vehicle. **Happy path:** sign in → start vehicle registration → identify type and descriptors → validate applicable VIN/plate policy → create immutable VehicleId → obtain an explicit access grant → open vehicle dashboard. **Must not assume:** registration automatically proves legal ownership. **Failures:** duplicate identity claim, insufficient permission, unsupported identifier, uncertain owner claim. **Evidence:** UC-VR-01, UC-IAM-03.

### J-02: Record home maintenance
**Actor:** caretaker. **Trigger:** oil/filter service. **Happy path:** select vehicle → select completed maintenance → record date, observed mileage, parts/labor/cost/provenance → save immutable completed service fact → Maintenance publishes MaintenanceCompleted via Outbox → Registry consumes and evaluates observation → UI shows synchronization status if relevant. **Failures:** invalid date/units, duplicate submission, delayed Kafka delivery, conflicting odometer observation. **Evidence:** UC-MNT-02, UC-VR-05.

### J-03: Receive maintenance due reminder
**Actor:** caretaker. **Trigger:** due-by-time/distance evaluation. **Happy path:** configure plan → authoritative odometer observations and time advance → Maintenance evaluates due state → notification request event → Notifications honors recipient preferences and dedup → user views due work. **Failures:** missing mileage, outdated reading, delivery rejected, opt-out. **Evidence:** UC-MNT-04, UC-NTF-01.

### J-04: Workshop booking with parts
**Actor:** service advisor. **Trigger:** request workshop visit. **Happy path:** authorized customer/vehicle lookup → create pending order → reserve required stock → reserve appointment → confirm workflow only when required outcomes succeed → show booking result. **Failures:** insufficient stock, slot unavailable, timeout, compensation failure. **Evidence:** UC-MNT-01, UC-INV-03, UC-SCH-01. **Status:** proposed Saga, not yet approved for all orders.

### J-05: Complete workshop work
**Actor:** technician/advisor. **Trigger:** performed service. **Happy path:** validate order state and assignments → enter work, parts, mileage, timestamp → consume reserved stock if applicable → finalize accepted service record → publish completed fact → Registry records odometer observation → history becomes visible only to authorized audiences. **Failures:** mismatched parts, partial completion, stock race, lost connectivity, duplicate command. **Evidence:** UC-MNT-03, UC-INV-04.

### J-06: Transfer one co-owner's interest
**Actor:** authorized party/administrator. **Trigger:** legitimate ownership transfer. **Happy path:** identify source OwnershipId → verify rights/evidence → atomically close source and establish destination with effective date → retain other co-owners → recompute access and QR grants per policy. **Failures:** simultaneous transfer, historical overlap, wrong source, pending verification. **Evidence:** UC-VR-03, UC-IAM-04.

### J-07: Publish and revoke public QR
**Actor:** authorized manager. **Trigger:** share limited vehicle info. **Happy path:** select allowed fields → issue random token and public projection → scan as visitor → inspect only approved fields → revoke/rotate token → verify old token fails. **Failures:** expired grant, leakage, unauthorized field, stale projection. **Evidence:** UC-PUB-01/02.

### J-08: Workshop asks for prior history
**Actor:** service advisor. **Trigger:** intake from a new customer. **Happy path:** request access → customer/grant policy evaluated → receive only authorized summary or explicitly shared documents → document access audited. **Failures:** another workshop's confidential invoice, legal owner without account, denied consent, revoked access. **Evidence:** UC-IAM-04.

## Journey quality gate
Review each with: happy path; no-data; invalid input; permission denied; duplicate request; concurrency conflict; asynchronous pending; partial failure; mobile/keyboard accessibility; privacy boundary.
