# Environment, Configuration, and Secrets Strategy

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Environments
| Environment | Purpose | Rules |
| --- | --- | --- |
| Local | Development/integration of selected modules | Synthetic data; isolated Kafka/DB; no prod secrets |
| CI | Reproducible verification | Ephemeral DB/Kafka when required; least privilege |
| Staging | Release rehearsal | Production-like contracts; isolated data; migration tests |
| Production | Real users/business data | Audited access, backup, recovery, monitoring |

## Configuration policy
Store code and configuration schemas in Git, **not credentials**. Use environment injection and validated startup configuration. Document each variable's owner, type, default (if safe), sensitivity, source and rollout implications. Never make `NODE_ENV=production` the sole security boundary.

## Secrets
DB credentials, Kafka SASL/TLS material, OIDC private keys, media signing keys, provider API keys and JWT secrets require a secret manager or equivalent protected runtime injection, rotation plan and least-privileged access. No plaintext secrets inside `.specify`, `.claude`, application logs or committed files.

## Service isolation
Logical databases and schemas per owning service; separate migration principal or controlled migration role; topic permissions only for approved producers/consumers; object storage access restricted by service/tenant policies.

## Local reliability
Development can run a subset of services with explicit test/fake adapters, without secretly falling back to shared production DBs. Seeded scenarios include owner + 3 co-owners, completed maintenance, stock race and revoked QR.

## Database migrations
Each service has independent migration history and compatible rollout. Expand-contract when multiple software versions coexist. Staging verifies forward migration and, when feasible, rollback/recovery strategy. Do not apply one service's migrations to another's DB.

## Promotion gates
Image digest/version, dependency validation, schema/contract compatibility, migration readiness, smoke checks, security approval, backup posture and rollback runbook. Approved automation toolchain remains open under ADR-0013.

## Outstanding
Environments' hosting topology, provider, secrets backend, tenancy isolation, network policy, preview environments and disaster recovery geography remain to be decided.
