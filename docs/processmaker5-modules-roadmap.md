# ProcessMaker 5 + ProcessMaker Mobile: Modular Rebuild Roadmap

## Objective
Rebuild ProcessMaker from core concepts with a modern architecture focused on:
- Low latency at high process volume.
- JSON-first data model.
- Better analytics and near-real-time operations.
- Parallel delivery in web (Angular + TypeScript) and mobile (Flutter + Dart).

## Recommended Data Platform (Optimal NoSQL Option)

### Primary recommendation: PostgreSQL + JSONB + Event Store pattern
Even if the target is "NoSQL-like JSON-first", the most balanced option for process engines is PostgreSQL using JSONB.

Why this is optimal:
- Native JSONB with indexing (GIN) for flexible process payloads.
- Strong ACID consistency for tokens/tasks/process state transitions.
- SQL analytics support without exporting all data elsewhere.
- Partitioning and materialized views for high-volume operational reporting.
- Easier migration path from current relational assumptions than pure document stores.

### Secondary option: MongoDB Atlas
A strong alternative for fully document-centric workloads and very dynamic schemas.

Tradeoff:
- Great flexibility and horizontal scale.
- More care required for complex transactional consistency across process state and token orchestration.

## Target Architecture (High level)
- **Frontend Web**: Angular + TypeScript.
- **Frontend Mobile**: Flutter + Dart.
- **Backend API**: TypeScript services (modular monolith first, microservices-ready boundaries).
- **Data**:
  - Primary process data in JSON documents.
  - Optional MySQL compatibility boundary for legacy integrations only.
- **Messaging**: Event bus (Kafka/RabbitMQ) for async execution and analytics feed.
- **Cache/Realtime**: Redis + WebSocket/SSE for live updates.
- **Observability**: OpenTelemetry + centralized logs + metrics + traces.

## Module Plan

### Module 1 — Product Discovery and Domain Mapping
- Define process engine capabilities to keep from current version.
- Map entities: process requests, request tokens, tasks, users, groups, drafts, versions.
- Define duplication rules (intentional snapshots vs accidental duplicates).
- Deliverable: canonical domain map and bounded contexts.

### Module 2 — Data Strategy and JSON Schema Governance
- Define JSON contracts for request data, task data, token data, draft/version snapshots.
- Versioned schemas with backward compatibility policy.
- Set retention, archival, and audit policies.
- Deliverable: schema registry and validation rules.

### Module 3 — Workflow Engine Core (Execution Runtime)
- Token lifecycle, routing, transitions, timers, escalations, retries.
- Idempotency and concurrency controls to avoid duplicate execution.
- Compensation and rollback strategy for failed transitions.
- Deliverable: runtime core and state machine definitions.

### Module 4 — Task and Request Service Layer
- APIs for create/read/update/complete task and request lifecycle.
- Fine-grained permissions based on users/groups/roles.
- Draft and version handling with controlled duplication.
- Deliverable: secure API contracts and service boundaries.

### Module 5 — Angular Web Platform Foundation
- Angular workspace setup, module boundaries, state management strategy.
- AuthN/AuthZ integration and secure session handling.
- Shared design system and normalized UI comments/documentation in English.
- Deliverable: base web shell with authenticated navigation.

### Module 6 — Flutter Mobile Foundation
- Flutter app architecture (feature-first modules).
- Secure storage, auth flow, offline cache policy.
- Shared domain contracts with backend generated models.
- Deliverable: base mobile shell with core navigation and login.

### Module 7 — Process Modeling and Builder UX
- Process designer, form builder, and version publishing flow.
- Draft mode, compare versions, controlled promote-to-live flow.
- Validation rules before deployment.
- Deliverable: MVP modeling module.

### Module 8 — Realtime Operations and Performance Optimization
- Live task queues, process state streaming, push notifications.
- Query optimization for high-volume task lists and dashboards.
- Caching, precomputed views, and pagination/search strategy.
- Deliverable: performance baseline meeting target SLAs.

### Module 9 — Analytics and Intelligence Layer
- Operational analytics (cycle time, SLA breaches, throughput, bottlenecks).
- Event-driven data pipeline for near-real-time dashboards.
- Foundations for predictive analytics and anomaly detection.
- Deliverable: analytics APIs + dashboard dataset definitions.

### Module 10 — Security, Privacy, and Compliance
- Data encryption at rest/in transit.
- Tenant isolation and RBAC/ABAC strategy.
- Audit trails, traceability, and compliance controls.
- Deliverable: security hardening checklist and controls implemented.

### Module 11 — Legacy Migration and Coexistence Bridge
- Migrate core process definitions and active requests.
- Coexistence strategy with optional MySQL legacy connectors.
- Data reconciliation and integrity verification.
- Deliverable: migration runbook and rollback plan.

### Module 12 — DevOps, Environments, and Release Engineering
- CI/CD for web, mobile, backend, and schema changes.
- Infrastructure as code for environments.
- Feature flags, canary releases, and incident response playbooks.
- Deliverable: production-ready delivery pipeline.

### Module 13 — QA, Testing, and Certification (Final Module)
- Unit, integration, contract, E2E, performance, security testing.
- Test data factories for JSON-heavy scenarios.
- Regression matrix for web and mobile parity.
- Go-live certification checklist and acceptance criteria.
- Deliverable: release quality gate for ProcessMaker 5 and ProcessMaker Mobile.

## Suggested Delivery Sequence
1. Modules 1–4 (domain + engine + API foundations).
2. Modules 5–6 in parallel (web and mobile foundations).
3. Module 7 (modeling UX), then Module 8 (performance).
4. Modules 9–10 (analytics + security).
5. Modules 11–12 (migration + release).
6. Module 13 as continuous track and final certification.

## Operating Standards for the New Program
- Create files/folders via command-line automation scripts.
- Keep code comments concise, standardized, and in English only.
- Enforce linting, formatting, and schema validation in CI.
- Prefer contract-first APIs and generated client SDKs.
