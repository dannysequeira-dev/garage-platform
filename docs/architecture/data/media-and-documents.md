# Vehicle Media and Supporting Document Storage

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Assets
Vehicle photos, registration evidence, maintenance receipts, workshop attachments, inspection photos, and public thumbnails. Media bytes are **not** stored inside Domain Aggregates or Kafka messages.

## Candidate design
Private object storage holds content; owning service stores an asset reference, classification, metadata, checksum, upload status, owner organization/resource, and retention policy. A short-lived signed upload/read URL can be issued only after policy evaluation; signed URLs are bearer permissions and must not be logged or exposed inadvertently.

## Upload flow
1. Caller requests scoped upload intent (auth + resource permission).
2. Backend issues limited one-time/short-lived upload capability.
3. Client uploads with size/content-type constraints and checksum.
4. Backend verifies object, file type, malware scanning status as required, ownership and metadata.
5. Domain associates approved asset by immutable reference.
6. Any public thumbnail must be explicitly transformed and reviewed against field-level policy.

## Lifecycle
`Initiated -> Uploaded -> Scanning -> Available` or `Rejected/Quarantined`; deletion should be auditable and coordinate active references, legal retention and garbage collection. Unverified uploads never become public.

## Security
Validate actual bytes (not only file extension), enforce size/count quotas, segregate tenant scopes, block SVG/script/content sniffing exposures where relevant, use restrictive content disposition, do not serve user attachments from privileged application origin without protection.

## Public QR
Grant scope determines which asset variant may be visible; owner-private invoice and registration document never become public by accident. Revoke/scope change must stop future signed read issuance, while acknowledging already copied data cannot be retracted.

## Decisions
Storage provider, bucket layout, scanning, retention, signed URL TTL, thumbnail pipeline, encryption/key rotation, quotas, GDPR/local data rights and legal evidence authenticity remain open.
