# OpenAPI Contracts - Proposed Design Artifacts

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
This directory contains **illustrative contract proposals**, not deployed or approved APIs. Accepted ADR-0009 requires explicit, versioned interfaces and generated TypeScript clients isolated behind frontend adapters.

## Included
- `garage-platform.v1.proposed.yaml`: small cross-context example covering vehicle registration, reads, maintenance entry, and QR token use.

## Not an approved gateway
The example combines logical resources in one OpenAPI document for review. It does **not** approve an API Gateway/BFF, merge service databases, or imply all endpoints share one deployed Spring Boot process. Split contracts by owning service once boundaries are ratified.

## Contract approval gate
Confirm resource model, auth and tenancy, dates, decimal representation, idempotency, API versioning, errors, private/public scopes, pagination, compatibility and data minimization. Then rename and publish an accepted contract and generate clients reproducibly.

## Security
Example QR public view uses a token header to avoid embedding secrets in ordinary API paths; how a scanned QR's landing page conveys its token remains open and requires privacy review.
