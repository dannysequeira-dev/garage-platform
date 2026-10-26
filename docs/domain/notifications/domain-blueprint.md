# Notifications - Domain Blueprint

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Mission
Deliver approved messages to eligible recipients; do not independently decide whether preventive maintenance is due. Maintenance owns due calculations.

## Candidate entities
`NotificationIntent` keyed by business reason/recipient/template, `DeliveryAttempt` for each provider attempt, `DeliveryStatus`, `RecipientPreference` (ownership TBD), `TemplateVersion`, `Channel`.

## Proposed flow
MaintenanceDueDetected event → contract/recipient policy → build deduplicated notification intent → check preference/consent → schedule channel delivery → persist attempt outcome → retry transient failure with cap → surface terminal/recoverable outcome.

## Idempotency design
Dedup by business fingerprint `(recipient, planId, dueThresholdVersion, channel, reminderStage)`, not solely by transport `eventId`. Replayed events must not resend an entire campaign. A forced resend is a separate auditable command.

## Delivery semantics
Provider accepted message != user received/read it. Distinguish queued, sent-to-provider, provider-rejected, delivered (if externally confirmed), bounced, cancelled, opt-out. Avoid pretending to guarantee human delivery.

## Security and compliance
Consent, opt-out and data-processing requirements depend on chosen jurisdiction. Store minimal destination data, encrypt sensitive fields, redact transport logs; no private vehicle/owner details in push preview by default. Provider credentials must remain secret.

## Open decisions
Initial channels (email/push/SMS), template language, preference owner, quiet hours, frequency capping, provider integration, retention and re-engagement policy.
