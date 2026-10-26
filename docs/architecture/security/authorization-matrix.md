# Proposed Authorization Matrix

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Reading
`A`=eligible after resource-level policy; `R`=read if explicitly scoped; `D`=deny by default; `T`=requires additional approved transaction/consent. **This is a proposal, not a permission grant.** A role alone is never sufficient without resource context.

| Action | Vehicle caretaker | Legal Party without account | Workshop technician | Workshop advisor | Workshop manager | QR visitor | Platform operator |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Register vehicle | A | D | T | T | T | D | T |
| View authorized vehicle | R | D | R | R | R | R: public only | T |
| Change VIN/plate | T | D | D | T | T | D | T |
| Add/transfer co-owner | T | D | D | T | T | D | T |
| Add self-reported maintenance | A | D | T | T | T | D | D |
| See private workshop record | R/T | D | R: assigned | R: assigned | R: org scope | D | T |
| Complete workshop service | D | D | A: assigned | A: assigned | A: org scope | D | D |
| Reserve stock | D | D | T | A: org scope | A: org scope | D | D |
| Adjust inventory quantity | D | D | D | D | A: org scope | D | T |
| Confirm workshop appointment | T | D | D | A: org scope | A: org scope | D | D |
| Issue/revoke public QR | T | D | D | T | T | D | T |
| View private legal ownership evidence | T | D | D | D | T | D | T |
| Manage org membership | D | D | D | D | A: org scope | D | T |

## Critical exceptions and dependencies
- `R` must be evaluated against specific field/record provenance; a shared summary is not a full invoice.
- QR visitor has only a public projection after valid bearer grant; cannot use authenticated APIs.
- Tech/advisor `assigned` implies job assignment and org membership, not all vehicles in registry.
- Legal Party without verified account cannot authenticate, even if PartyId belongs in ownership table.
- Owner transfer may alter access only after approved policy process; do not instantly rewrite every third-party permission without review.
- Platform emergency access requires separate approved audit/break-glass workflow.

## Test IDs to include
AUTH-01 no foreign organization data; AUTH-02 no implicit VIN-based lookup; AUTH-03 revoke membership while session active; AUTH-04 caretaker vs legal Party; AUTH-05 QR field isolation; AUTH-06 private document signed URL after revocation; AUTH-07 owner transfer with co-owner remaining; AUTH-08 support access audit; AUTH-09 ownership share grant expiration; AUTH-10 self-reported vs workshop confidential service note.

## Review fields
Expand every `A/R/T` into exact roles, data conditions, authorized source, policy version, audit and denial behavior before implementation. Resolve OD-003, OD-017, OD-018, OD-024 and OD-028.
