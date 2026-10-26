# Authoritative Data Ownership and Data Flow Matrix

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Rule
One context owns each business fact. Consumers may hold scoped, source-attributed projections but cannot mutate source databases. Per-service PostgreSQL logical isolation does not require a physical PostgreSQL instance per service.

| Data | Authority | Allowed derived consumers | Direct write by others |
| --- | --- | --- | --- |
| Vehicle identity, VIN/plate | Vehicle Registry | Maintenance/Public Access (approved subset) | Forbidden |
| Temporal legal ownership | Vehicle Registry | Security policy, permitted audit | Forbidden |
| Odometer observations and current projection | Vehicle Registry | Maintenance plan engine | Forbidden |
| Completed service record/provenance | Maintenance | Public Access (redacted), owner UI | Forbidden |
| Maintenance order and due state | Maintenance | Scheduling/Notifications as contract | Forbidden |
| Part catalog and stock ledger | Inventory | Maintenance, Commerce future | Forbidden |
| Stock reservation | Inventory | Maintenance Saga | Forbidden |
| Appointments, resource availability | Scheduling | Maintenance | Forbidden |
| QR grants, hashes, authorized projection | Public Access | Public visitor only via scope | Forbidden |
| Notification intents and attempts | Notifications | Operations/audit | Forbidden |
| User/session identity | Identity provider/context TBD | Permission checks | Forbidden |
| Org membership and resource grants | Organization/Auth TBD | All protected services | Forbidden |

## Privacy classification
- **Public candidate:** sanitized model/type and explicit approved service summary only.
- **Internal business:** work orders, operational stock counts, appointment timing, cost breakdowns.
- **Private personal:** legal ownership, phone/email, identity evidence, private service notes.
- **Secret:** credentials, QR bearer token, OIDC signing secrets, provider API keys.

## Projection rules
Store origin (`sourceContext`, `sourceRecordId`, `sourceRevision`), processing timestamp and permission scope. If access changes, suppress previously visible fields immediately at read policy boundary. Do not treat deletion of a consumer projection as deletion of authoritative historical evidence.

## Retention and corrections
Correct through authorized events/record revisions and audit; privacy deletion may require different retention behavior based on legal/regulatory obligations. Approved jurisdiction policy required; do not claim indefinite retention is lawful.
