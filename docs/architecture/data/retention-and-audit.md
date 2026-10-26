# Data Retention, Corrections, Audit, and Privacy

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
Traceability must not mean unbounded storage of personal data. Actual retention periods, deletion rights and cross-border transfer rules require jurisdiction-specific legal review (OD-024).

## Data classes
| Class | Examples | Treatment |
| --- | --- | --- |
| Business history | Completed work, odometer observations, stock movements | Append/correction provenance, policy-based retention |
| Personal data | Owner identities, membership, phone/email | Purpose limitation and controlled access |
| Sensitive evidence | Identity/title photos, private repair notes | Stronger access, limited retention and audit |
| Security secrets | QR raw token, API keys, sessions | Never log; rotate/revoke; minimal retention |
| Telemetry | Traces, logs, metrics | Short operational retention; redact identities |

## Audit events
Capture actor/principal type, organization context, authorized action, resource type/opaque ID, timestamp, correlation, outcome and reason code for sensitive transitions. Avoid including full record payloads and secrets in audit logs. Audit logs need tamper-evident handling as appropriate to risk.

## Corrections
Domain corrections should preserve original business fact, correction source/reason and effectivity. Avoid `DELETE` plus silent reinsert of legal ownership history and completed maintenance if auditability is required. Privacy erasure may still mandate irreversible deletion/anonymization under applicable law; that is a separate legal process.

## Data subject operations
Design workflows for access/export, rectify, object/revoke sharing, remove account, delete eligible material and audit subject-rights handling. Clarify whether a Party who owns a vehicle can request another workshop's proprietary record.

## Backups
Backup retention must not conflict with deletion policy; define expiration, disaster recovery restoration protections, and post-restore replay of erasure/revocation requests.

## Approval gate
Obtain legal/operational review of retention matrix per jurisdiction, purpose, data category, owner, storage, encryption, deletion method and evidence requirements. No numeric retention duration is invented here.
