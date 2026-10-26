# Scheduling - Availability, Capacity, and Time Semantics

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Time model
Use absolute instants for stored appointment boundaries (`startAt`, `endAt`), and an explicit named IANA timezone for local workshop hours and rules. A *local date* is not a UTC timestamp. Calendar arithmetic must account for timezones and DST where relevant; never assume UTC offsets are fixed globally.

## Candidate availability equation
`availableCapacity(slot) = configuredCapacity(slot) - activeReservations(slot)` for a resource class. Real resource constraints may require non-overlapping intervals, not just counters. For a single lift, two overlapping reservations are illegal even if each query individually found it available.

## Proposed policies
- `endAt > startAt`, positive service duration.
- Rescheduling must atomically release old slot and reserve new slot within Scheduling's local transactional boundary, or use an explicit multi-step recoverable model.
- Cancelled appointments do not consume future capacity, but preserve audit.
- A held capacity slot must have an expiry policy if holds are introduced.
- Appointments have a service reference by ID, not an imported Maintenance aggregate.
- Appointment confirmation should never infer stock readiness; Saga/maintenance workflow decides overall booking.

## Concurrency choices
- Database exclusion constraints for overlapping resource intervals where supported.
- Optimistic version or locked capacity bucket for pooled resources.
- Idempotent booking command outcomes to avoid duplicate confirmation.

## Test matrix
Simultaneous slot requests, adjacent boundaries, same-time rescheduling, cancelled appointment rebooking, daylight-saving transitions, offline reconciliation, double-confirmed command, late Saga response, capacity reduction below currently reserved demand.

## UX projection
Available slots are snapshots and may become unavailable before user confirms. UI must surface final confirmation failure and refreshed choices without pretending a preliminary list is a guaranteed reservation.
