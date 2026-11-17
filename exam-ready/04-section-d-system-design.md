# Section D — System Design & Architecture (15 marks) — Ranked Model Answers

**Rank:** ★★★ = almost certain (PKSF context: Partner Organization (PO) data, DSS, reporting) · ★★ · ★.
15 marks, 15 minute → **diagram + 5-6 bullet heading**. Prothome eta mukhosto kore nao:

## The answer template (marks-wise)
| Part | Marks | Ki likhbe |
|---|---:|---|
| 1. Requirements & assumptions | 2 | functional + non-functional (users, volume, availability, security) |
| 2. Architecture diagram | 4 | components + data flow, label security boundary |
| 3. Data model / API | 3 | key tables/endpoints, tenant column |
| 4. Security & tenant isolation | 3 | authN/authZ, RLS, audit |
| 5. Reliability & scale | 2 | queue, cache, backup, monitoring |
| 6. Trade-offs | 1 | 1–2 line "why this, not that" |

| # | Design question | Rank |
|---|---|---|
| D1 | Centralized Decision Support System (DSS) for partner organizations | ★★★ |
| D2 | Secure multi-tenant API & authentication flow | ★★★ |
| D3 | Monthly data submission & validation pipeline | ★★ |
| D4 | Reporting system for millions of rows | ★★ |
| D5 | High availability and disaster recovery | ★★ |
| D6 | Secure document upload | ★ |
| D7 | Resilient integration with an external system | ★ |
| D8 | Monolith vs microservices (and migration/zero-downtime DB change) | ★ |

---

## D1 ★★★ Design a centralized Decision Support System (DSS) for partner organizations

**Scenario:** Hundreds of partner organizations (POs) submit monthly financial/operational data; PKSF central users see consolidated dashboards and reports; a PO must see only its own data.

**1. Requirements**
- Functional: PO data submission (form + Excel/CSV upload), validation, approval workflow, consolidated dashboards, ad-hoc & scheduled reports/export, user & role management, audit trail.
- Non-functional: security & tenant isolation, ~99.9% availability, reports on millions of rows within seconds, data retention, auditability, backup/DR.

**2. Architecture**
```
 PO user / PKSF user (browser)
        │ HTTPS
 [React SPA] ──► [CDN/WAF/Load Balancer]  ◄── security boundary
                        │
                 [API Gateway / Ingress]  (rate limit, TLS)
                        │
       ┌────────────────┴──────────────────────────┐
       │        Spring Boot (modular monolith)      │
       │ Auth/Tenant ctx │ Submission │ Reporting │  │
       │ Users/Roles │ Notification │ Audit        │
       └───────┬────────────┬──────────────┬────────┘
               │            │              │
        [PostgreSQL     [Redis cache]  [Message queue]──►[Workers: validate,
         primary + RLS]  (tenant-scoped)                    import, report/export]
               │                                              │
        [Read replica /                                [Object storage:
         materialized views]                            uploaded files, exports]
               │
     [Backup + WAL archive]      [Logs/Metrics/Traces → Grafana/ELK]
        Identity: Keycloak/OIDC (Authorization Code + PKCE, MFA)
```

**3. Data model (core tables)**
`organizations`, `users`, `memberships(user_id, organization_id, role)`, `roles`, `reporting_periods`, `submissions(id, organization_id, period_id, status, version)`, `submission_rows`, `validation_errors`, `audit_events`. **Every tenant-owned row carries `organization_id`**; unique/index with tenant scope, e.g. `UNIQUE (organization_id, period_id, form_type)`, index `(organization_id, period_id)`.

**4. Security & tenant isolation**
- OIDC login (Authorization Code + PKCE), short-lived JWT, MFA for privileged users.
- **Tenant scope derived server-side** from the user's membership — never from the request body.
- Enforce in service and repository layers (Hibernate filter) **+ PostgreSQL Row-Level Security** as defense in depth.
- Central (PKSF) cross-tenant access only through explicit privileged roles; every such access audited.
- Tenant-scope cache keys, exports, background jobs, file paths (`/org/{id}/...`).
- Encryption in transit (TLS) and at rest; secrets in a vault; no PII in logs.

**5. Reliability & performance**
- Uploads processed **asynchronously**: API returns `202 Accepted` + job id; worker validates, imports in batches, stores row-level errors.
- **Idempotent** submissions (idempotency key / unique constraint); outbox pattern for events; bounded retries with jitter; dead-letter queue.
- Dashboards: pre-aggregated tables/materialized views refreshed on schedule/event; indexes; Redis for hot, measured queries only.
- Backups + PITR, tested restores, stated RPO (e.g., ≤ 15 min) / RTO (e.g., ≤ 1 h); multi-instance stateless app behind LB; health checks.

**6. Trade-offs:** modular monolith first (simple transactions, cheaper ops) instead of microservices; shared schema with RLS instead of DB-per-PO (cheaper; schema-per-tenant only if regulation demands stronger isolation).

---

## D2 ★★★ Design a secure multi-tenant API

```
Client ─► Gateway (TLS, rate limit, JWT signature/exp/iss/aud check)
        ─► Controller (@PreAuthorize role)
        ─► Service (TenantContext from token → membership lookup)
        ─► Repository (always filtered by organization_id / RLS)
        ─► DB
```
- Authentication: OIDC; validate JWT (signature via JWKS, `exp`, `iss`, `aud`).
- Authorization = **role (what) + scope (which organization)**; deny by default.
- `tenant_id` from the client is never authority. Prevent IDOR: `GET /submissions/{id}` must check the row belongs to the caller's organization (return 404/403).
- Negative tests: user A cannot read/update/export user B's records through any endpoint, cache, job or file URL.
- Protections: input validation, DTOs (avoid mass assignment), rate limiting, audit log, CORS allow-list, secure headers, dependency scanning.

---

## D3 ★★ Monthly data submission & validation pipeline
`Upload → store raw file (object storage) → enqueue job → worker parses → schema validation → business-rule validation → write staging → approve → promote to final tables → notify`.
Key points: `202 Accepted` + status endpoint; **idempotency** (file hash/period key); partial failure → row-level error report (downloadable); batch inserts (`COPY`/JDBC batch); staging tables so bad data never touches reports; reconciliation (row counts, control totals); retry + DLQ; audit who/when; reopen/correction workflow with versioning.

---

## D4 ★★ Reporting system for millions of rows
Filter and aggregate **in PostgreSQL**, not in Java memory. Appropriate indexes (composite, partial), partition large tables by period/organization, `EXPLAIN ANALYZE` to tune. **Keyset pagination** for browsing; **async export jobs** (CSV/XLSX streamed to object storage, download link expires) for big files; **materialized views / summary tables** for repeated dashboards; read replica only after measuring a bottleneck; cache hot reports with tenant-scoped keys; guard with query timeouts and limits.

---

## D5 ★★ High availability and disaster recovery
- App: ≥2 stateless instances behind a load balancer; health/readiness checks; autoscaling; rolling/blue-green deploys.
- DB: primary + synchronous/async standby with automated failover; connection pooling (PgBouncer/HikariCP).
- Data: daily base backup + continuous WAL archiving → point-in-time recovery; off-site, encrypted copies; **restore drills**.
- State RPO/RTO, runbooks, monitoring and alerting, DR site/region for severe failures, documented ownership.

---

## D6 ★ Secure document upload
Authenticate + authorize → limit size → allow-list extensions **and verify file signature (magic bytes)** → generate server-side file names (no path traversal) → store outside the web root/in object storage (private by default) → malware scan → tenant-scoped path → short-lived signed URLs for download → audit access, retention policy.

## D7 ★ Resilient integration with an external service
Timeouts; retry only transient errors with exponential backoff + jitter; circuit breaker + bulkhead; idempotency keys for writes; queue long work; DLQ + reconciliation job; correlation IDs; contract tests; fallback/degraded mode.

## D8 ★ Monolith vs microservices; zero-downtime DB change
Start as a **modular monolith**; split only on stable domain boundary + real scaling/team need (cost: network failures, distributed data, observability). **Expand-and-contract migration:** add new nullable column/table → deploy code that writes both and reads old/new → backfill in batches → switch reads → remove the old column after old versions retire (Flyway/Liquibase, rehearse, rollback plan).

---

## Quick self-test (aaina-r shamne bolo, 2 minute proti-ta)
1. Draw D1 architecture and name 3 tenant-isolation layers (app filter, RLS, tenant-scoped cache/files).
2. Why 202 + queue for uploads? Why staging table?
3. How do you stop PO-A reading PO-B's report export?
4. What are RPO/RTO of your design and how do you prove them? (restore drill)
