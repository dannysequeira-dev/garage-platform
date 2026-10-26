# Inventory - Reservation and Consumption Sequence

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Scope
Demonstrate local atomicity, concurrency and business idempotency for proposed Inventory aggregates.

```mermaid
sequenceDiagram
  autonumber
  participant Caller as Maintenance Saga
  participant Listener as Inventory Message Adapter
  participant DS as Inventory Domain Service
  participant Bucket as StockBucket Aggregate
  participant TX as UnitOfWork
  participant DB as Inventory DB
  Caller->>Listener: ReserveStock(operationId, part, location, qty)
  Listener->>DS: Reserve command
  DS->>TX: Execute atomic action
  DS->>DB: Check operation identity + lock/version stock
  alt Previously completed matching command
    DB-->>DS: Return existing reservation/outcome
  else New command
    DS->>Bucket: reserve(qty)
    alt Insufficient available
      Bucket-->>DS: Domain rejection
      DS->>DB: Persist rejected idempotency result if required
    else Accepted
      Bucket-->>DS: Stock reserved
      DS->>DB: Persist bucket + reservation + movement/Outbox
    end
  end
  DB-->>TX: Commit or rollback
  DS-->>Listener: Stable logical outcome
  Listener-->>Caller: Async integration outcome via approved transport
```

## Critical parallel scenario
With onHand=3 and reserved=0, two concurrent requests for 2 units cannot both succeed without new supply. A read/modify/write sequence without locking/versioned constraint is insufficient. When consumed, decrease both onHand and reserved exactly once; releasing consumed stock is invalid.

## Tests
Parallel reservations, duplicate operation IDs with same/conflicting body, release twice, late release after consume, negative adjustment attempt, reservation expiry, missing part/location, DB rollback before Outbox and consumer replay after ack loss.
