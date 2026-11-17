# Section D — System Design & Architecture (Model Answers)

## 1. Design a centralized Partner Organization DSS
**Requirements:** Multiple partner organizations submit monthly data; central PKSF users see consolidated dashboards; partners must not see each other's records.

**Architecture:** React portal → HTTPS ingress/WAF → Spring Boot modular backend → PostgreSQL. Add a durable queue and workers for validation/import/report generation; Redis only for measured, tenant-scoped cache; object storage for uploaded source files; monitoring/logging/tracing platform.

**Data model:** organizations, users, memberships, roles, reporting_periods, submissions, submission_rows, validation_errors, audit_events. Every tenant-owned row carries organization_id. Add constraints and indexes that include tenant scope where appropriate.

**Security:** Authenticate with OIDC; derive organization scope from validated server-side membership, not request body; enforce authorization in service and repository query paths; consider PostgreSQL RLS as defence in depth; central cross-tenant reports require explicit privileged roles and audit trails. Scope cache keys, exports, background jobs and object-storage paths by tenant.

**Reliability:** Idempotency key for submissions, outbox pattern for event publication, bounded retries with jitter, dead-letter queue and reconciliation. Backups, point-in-time recovery, restore drills, defined RPO/RTO.

## 2. Design a secure multi-tenant API
Use a tenant context established by authentication middleware. Every repository method receives or derives tenant scope. Never trust tenant_id supplied by client as authority. Add cross-tenant negative tests, audit privileged access, and ensure exports and caches apply identical checks.

## 3. Design a reporting system for millions of rows
Filter and aggregate in PostgreSQL; use appropriate indexes and query plans; avoid loading all rows into application memory. Use keyset pagination for interactive browsing, asynchronous export jobs for large files, materialized views/pre-aggregation for repeated expensive reports and read replicas only after measuring bottlenecks.

## 4. Monolith vs microservices
A modular monolith is simpler for early development, deployment and transactions. Microservices enable independent deployment/scaling and ownership but introduce network failures, distributed data, observability and operational complexity. Split only around stable domain boundaries with a clear business or scaling reason.

## 5. Design a resilient external integration
Set connection/read timeouts; retry only transient errors with bounded exponential backoff and jitter; use circuit breakers and bulkheads; use idempotency for writes; put long work on a durable queue; provide dead-letter handling, reconciliation and correlation IDs.

## 6. High availability and disaster recovery
Use multiple stateless app instances behind a load balancer; monitor health/readiness; configure database replication/failover; maintain independent backups and point-in-time recovery; test restore/failover regularly. State measurable RPO/RTO and document operational ownership.

## 7. Low-downtime database migration
Use expand-and-contract: add backward-compatible columns/tables, deploy code that can read old and new forms, backfill in batches, validate invariants, switch reads/writes, then remove old schema only after old application versions are retired. Rehearse, monitor and keep rollback options.

## 8. Secure document upload
Authenticate and authorize; limit size; allowlist formats; inspect file signatures; generate server-side filenames; prevent path traversal; store outside executable web roots; scan malware; keep private by default; log access and retention; use short-lived signed URLs when appropriate.

## 9. Observability plan
Use structured logs with correlation IDs, metrics for latency/error/traffic/saturation, distributed traces for cross-service requests, dashboards and actionable alerts. Never log access tokens, passwords or unnecessary sensitive records.

## 10. Architecture trade-off answer template
1. Clarify functional and non-functional requirements.
2. State scale and assumptions.
3. Draw components and data flow.
4. Explain schema, APIs and consistency.
5. Explain identity, authorization and tenant isolation.
6. Explain caching, queues, scaling and failure handling.
7. Explain deployment, observability, backup and recovery.
8. State trade-offs, risks and what you would measure.
