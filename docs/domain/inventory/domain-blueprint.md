# Inventory - Tactical Domain Blueprint

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Mission
Own catalog identities, physical stock ledger, reservations, and consumption. Maintenance may request a reservation but cannot change stock directly.

## Candidate aggregates
| Candidate | Responsibility | Key invariant |
| --- | --- | --- |
| Part | SKU, description, compatible product metadata | SKU unique in chosen organization namespace |
| StockBucket | Part + location stock and concurrency guard | No overselling / negative available |
| InventoryReservation | Idempotent hold and lifecycle | Reserve <= available, release once |
| StockMovement | Append-only recorded stock fact | Every quantity change attributable |

## Quantity vocabulary
`onHand` physical stock, `reserved` committed available holds, `available = onHand - reserved` when no other holds. Quantities must have explicit units. Consumption of reserved stock should decrease `onHand` and `reserved` consistently. Corrections use authorized StockAdjustment rather than silent mutation.

## Candidate commands
`RegisterPart`, `ReceiveStock`, `ReserveStock(operationId, partId, locationId, quantity, orderRef)`, `ReleaseReservation`, `ConsumeReservation`, `AdjustStock`, `TransferStock` (multi-location only if approved).

## Failure / compensation
A failed appointment after accepted reservation triggers a compensating ReleaseReservation request; it is not a rollback of the earlier committed reservation. Release must tolerate retries and late messages, and must not release consumed stock. A compensation failure must create a visible recoverable state.

## Events
Proposed `StockReceived`, `StockReserved`, `StockReservationRejected`, `ReservationReleased`, `StockConsumed`, `StockAdjusted`. Schemas are not approved.

## Security and tenancy
Catalog SKU uniqueness, stock locations and permission scopes depend on Organization/Tenant choice (OD-018/030). A staff member may have read access to stock but not adjustment rights. Provenance is required for material adjustments and loss.

## Critical tests
Concurrent reservations of last unit; duplicate reserve with same logical command ID; consume twice; release after consume; insufficient stock; stock receipt while reservation in flight; negative/zero quantities; transfer atomicity if supported; stock adjustment audit.
