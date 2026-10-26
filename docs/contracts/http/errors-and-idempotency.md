# HTTP Error Model, Concurrency, and Idempotency

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Error response proposal
Align with Problem Details (RFC 9457), adding domain-independent machine-readable `code` and opaque `traceId`:
```json
{
  "type": "https://docs.example.invalid/problems/resource-conflict",
  "title": "Conflict",
  "status": 409,
  "detail": "The resource changed during this request.",
  "code": "VERSION_CONFLICT",
  "traceId": "opaque-trace-id"
}
```
Do not expose hidden record IDs, PII, SQL details, Java exception stacks, secrets or tenancy leakage in public problem responses.

## Proposed error taxonomy
| Error | HTTP mapping candidate | Handling |
| --- | --- | --- |
| Authentication missing | 401 | Authenticate |
| Resource permission denied | 403 or conceal as 404 | Enforce non-enumeration policy |
| Malformed transport payload | 400 | Validate transport |
| Invalid domain state or transition | 422 / 409 by reason | Stable domain error code |
| Optimistic concurrency mismatch | 409 / 412 | Refetch and retry with user input if needed |
| Idempotency-key payload mismatch | 409 | Do not process |
| Resource absent | 404 | Not visible vs missing policy |
| Dependent service temporarily unavailable | 503 | Bounded retry or async state |
| Unexpected server error | 500 | Correlation ID, internal safe logs |

## Idempotency semantics
For retryable commands, use an optional/required `Idempotency-Key` policy by operation. Scope to operation, organization/principal and time window. Persist request fingerprint + resulting business outcome transactionally as needed. Same key + same intent must yield same logical effect; same key + different content must be rejected.

## Concurrency
Support aggregate version/ETag or explicit conditional commands. `If-Match` is meaningful only if strong validators and representation semantics are consistent; do not treat a UI stale read as final stock availability.

## Correlation and audit
`traceId` is technical observability, not authorization. `operationId` (business command) differs from `eventId` (integration message) and `requestId` (network call). Preserve business correlation across outbox, Saga and consumer handling without logging bearer secrets.

## Tests
Replay same and changed idempotency requests, concurrent updates, fake foreign resource, invalid signed public grant, service failure, translated DomainError, and 500 log redaction.
