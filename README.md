# Garage Platform

> Working name — subject to change.

A vehicle lifecycle management platform designed around Domain-Driven Design, Hexagonal Architecture, Event-Driven Architecture, and distributed systems.

The platform manages vehicles, ownership history, maintenance, scheduling, spare parts, inventory, and controlled public access through QR codes.

It begins as a personal engineering project, with the long-term goal of becoming a commercially viable product.

## 1. Vision

Build a platform where vehicles are treated as independent entities with their own identities and histories, rather than merely as records belonging to a workshop.

A vehicle may have multiple owners over time, receive maintenance from different providers, consume inventory, and expose selected information through controlled public access.

The platform should support individual vehicle owners, workshops, and organizations without making any single actor the exclusive owner of a vehicle's complete history.

## 2. Core Capabilities

- Vehicle registration and identification.
- Multiple ownership relationships and historical traceability.
- Maintenance history and service records.
- Preventive maintenance based on time and mileage.
- Appointment scheduling and notifications.
- QR-based vehicle information access.
- Spare parts and inventory management.
- Inventory reservations, consumption, and sales.
- Multi-organization support and access control.

## 3. Architectural Direction

### Backend

- Java with Maven and parent POMs.
- Independently deployable microservices.
- Domain-Driven Design with rich domain models.
- Hexagonal Architecture and dependency inversion.
- Event-Driven Architecture using Apache Kafka.
- Transactional Outbox and Inbox patterns.
- Saga pattern for distributed business processes.
- Database ownership per microservice.
- Explicit transaction boundaries and domain invariants.

### Frontend

- TypeScript with a monorepo structure.
- Framework-independent domain and application logic.
- Ports and adapters for external integrations.
- Next.js as the initial web application.
- Future support for mobile and desktop applications.
- Shared design tokens, UI components, and presentation utilities.
- Backend-owned authoritative business rules.

### Infrastructure

- Independently deployable applications and services.
- Containerized development and deployment.
- PostgreSQL for relational persistence.
- Kafka for asynchronous integration.
- Distributed tracing, metrics, and structured logging.
- Infrastructure provisioning and configuration as code.

## 4. Engineering Principles

1. Business invariants belong to the domain.
2. Domain models must not depend on infrastructure frameworks.
3. Aggregate roots enforce consistency boundaries.
4. Entities expose behavior instead of unrestricted state mutation.
5. Value objects should be immutable.
6. Builders must preserve domain invariants.
7. Creation and reconstitution are distinct operations.
8. Infrastructure adapters implement domain-defined ports.
9. Distributed workflows must explicitly handle failures and compensation.
10. Backend services own authoritative business decisions.
11. Frontend application logic must remain portable across frameworks.
12. Shared modules must not become a source of domain coupling.
13. Architectural boundaries must be enforced through automated checks.
14. Architectural decisions must be documented and version-controlled.

## 5. Repository Organization

The repository will contain the following areas:

- `backend/` — Java microservices and shared backend modules.
- `frontend/` — TypeScript workspace, applications, domains, and shared UI.
- `contracts/` — Public API and event contracts.
- `infra/` — Infrastructure and deployment configuration.
- `docs/` — Architecture, decisions, domain definitions, and engineering guidelines.

Each backend service and frontend application must remain independently buildable and deployable.

## 6. Documentation

Architecture documentation is maintained as Markdown within this repository.

The documentation will cover:

- Architectural constitution.
- System and bounded-context design.
- Backend and frontend module structures.
- Domain modeling conventions.
- Event-driven integration.
- Transaction management and distributed consistency.
- API and messaging contracts.
- Observability and operational guidelines.
- Architecture Decision Records (ADRs).

The documentation is the reference for architectural intent, while the code and automated tests remain the executable source of truth.

## 7. Development Approach

Development follows incremental vertical slices.

Each slice should validate domain behavior, dependency boundaries, persistence, integration contracts, and application consumption.

The system may intentionally adopt advanced architectural patterns for learning and experimentation, but each abstraction must have a documented responsibility.

Complexity is acceptable when intentional, understood, and testable.

## 8. Project Status

**Current phase:** Architecture and foundation design.

Implementation has not started.

Architecture decisions are progressively documented and reviewed before major implementation work.

## 9. Licensing

Licensing and commercial distribution terms are not yet defined.

All rights reserved until an explicit license is selected.
