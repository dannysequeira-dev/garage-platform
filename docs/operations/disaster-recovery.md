# Backup, Restore, and Disaster Recovery Plan

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Purpose
Restore business integrity after DB loss, Kafka interruption, object-store failure, credential compromise or an accidental deployment. Concrete RTO/RPO require approved service criticality and cost expectations (OD-023).

## Data asset inventory
- Per-service PostgreSQL authoritative records and outbox/inbox/Saga state.
- Kafka persisted event streams and consumer offsets.
- Private media/ownership evidence.
- Configuration, IaC, schemas and encryption keys.
- Secure audit and business provenance.

## Proposed recovery order
1. Declare incident, isolate affected environment and protect logs/evidence.
2. Restore credentials and network access using safe secret recovery.
3. Restore each authoritative PostgreSQL database to consistent chosen point with verified constraints.
4. Restore/reconstruct object data and references; validate checksums/access controls.
5. Resume services using compatible image/schema versions.
6. Reconcile Outbox/Inbox and Kafka replay window to prevent lost or duplicated business effects.
7. Reconcile Saga state, pending reservations, appointments and odometer source observations.
8. Revalidate permissions, revoked QR grants and deletion requests to avoid resurrecting access.
9. Perform domain-specific smoke tests and declare recovery with audit.

## RPO/RTO matrix (blank pending approval)
| Asset | Proposed RPO | Proposed RTO | Restore proof |
| --- | --- | --- | --- |
| Vehicle Registry | TBD | TBD | Vehicle identity/ownership/history reconciliation |
| Maintenance | TBD | TBD | Completed record + outbox consistency |
| Inventory | TBD | TBD | Nonnegative stock and reservation replay |
| Scheduling | TBD | TBD | No double-booking after restore |
| Public Access | TBD | TBD | Revoked token remains revoked |
| Notifications | TBD | TBD | No duplicate message storm |
| Media | TBD | TBD | Checksums and permissions restored |

## Required drills
Missing recent outbox publisher, restore with consumer offset mismatch, late duplicate Kafka replay, corrupted ownership record, active Saga interrupted, QR grant revoked immediately before backup, secret compromise and cloud object deletion.

## Restrictions
Backups must be encrypted and access restricted; separate restore credentials, test restore regularly, retain as legally appropriate, and document offsite/cross-region expectations. Do not assume a backup job's green status means recovery will work.
