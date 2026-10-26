# Maintenance Booking - Proposed Saga Sequence and Failure Paths

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Status
This is an **illustrative Saga candidate**. Not every maintenance order needs stock or an appointment. Business workflow and compensation policy must be ratified (OD-010/015).

```mermaid
sequenceDiagram
  autonumber
  actor Client
  participant M as Maintenance Domain Service + Saga
  participant MO as Maintenance Outbox
  participant K as Kafka
  participant I as Inventory
  participant S as Scheduling
  Client->>M: RequestBooking(orderId, operationId)
  M->>M: Persist Pending Saga + command intent
  M->>MO: Record ReserveStock command message
  MO-->>K: Publish committed command
  K-->>I: ReserveStock
  alt Insufficient stock
    I-->>K: StockReservationRejected
    K-->>M: Handle rejection idempotently
    M->>M: Mark rejected
  else Stock reserved
    I-->>K: StockReserved(reservationId)
    K-->>M: Handle result idempotently
    M-->>K: RequestAppointment
    K-->>S: Request appointment
    alt Slot confirmed
      S-->>K: AppointmentConfirmed
      K-->>M: Confirm booking
      M->>M: Complete Saga
    else Slot unavailable
      S-->>K: AppointmentRejected
      K-->>M: Enter compensating
      M-->>K: ReleaseReservation
      K-->>I: Release reserved stock
      I-->>K: ReservationReleased
      K-->>M: Mark compensated failure
    end
  end
```

## Invariants and timeouts
Saga state and outgoing messages must be durable. Duplicate responses must not repeat effects. Late confirmations after timeout require reconciliation. Never mark the booking confirmed before both required conditions hold. Failed release must remain in `REQUIRES_INTERVENTION`, not silently disappear.

## Domain boundaries
Maintenance owns overall service booking decision; Inventory owns stock correctness; Scheduling owns appointment capacity. Saga commands are not completed business events. Outbox/Inbox support reliable messaging but do not replace Saga.
