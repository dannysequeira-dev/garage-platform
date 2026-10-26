# CI/CD, Build Independence, and Architecture Gates

**Status:** Draft - pending product/architecture review  
**Version:** 0.1.0  
**Prepared:** 2026-10-26

## Foundation
Single Git monorepo with independent backend Maven reactor and frontend pnpm workspaces. Each deployed artifact must be buildable, testable and released independently. One repository does **not** imply one deploy or one shared runtime.

## Candidate pipeline phases
1. **Identify affected graph:** changes in a common Java package must test all affected services; changes in shared TS packages test affected apps.
2. **Static checks:** formatting, Java/TS lint, security secrets scan, dependency and lock validation.
3. **Architecture gates:** ArchUnit, Maven dependency graph, forbidden TS imports and circular package checks.
4. **Unit tests:** Java Domain Core pure; TypeScript Core/Application without React.
5. **Integration tests:** PostgreSQL/Kafka with Testcontainers or equivalent, outbox/inbox atomicity, Saga failure, SQL migrations.
6. **Contracts:** OpenAPI/AsyncAPI schema validation, generated TS client compile, producer/consumer compatibility.
7. **Security:** SCA/container scan, IaC checks, least privilege and upload/QR authorization tests.
8. **Build:** reproducible per-service/app image with immutable digest and provenance.
9. **Staging rollout:** migrations, smoke tests, health/telemetry and backwards compatibility.
10. **Production promotion:** controlled release with rollback/forward-fix plan and alerts.

## Build and version rules
Parent Maven POM handles shared plugin/dependency management but not shared service deployment. Node/Java/pnpm versions require explicit verified toolchain decision; do not copy versions from unrelated projects. Shared artifacts must be versioned or built from pinned commit state.

## Failure gates
Domain compile importing Spring, DataAccess imported by Domain Service, duplicate event business effect, cross-org authorization leak, schema-breaking contract without migration, or failed DB-outbox atomicity test must block integration.

## Database rollout
Expand then migrate/backfill, deploy compatible readers/writers, remove legacy schema later. Restore/rollback is not equivalent to reversing arbitrary data migration. Do not depend on a synchronized all-service deployment for ordinary changes.

## AI agents and audits
Every agent reports changed modules, verified checks, failures and unverified behavior. No claim of successful tests not executed. Do not auto-commit, push or deploy without explicit authorization.

## Decisions
CI provider, artifact registry, semantic version strategy, release cadence, deployment orchestrator, ephemeral environment budget, SAST tools and DORA targets remain open under ADR-0013.
