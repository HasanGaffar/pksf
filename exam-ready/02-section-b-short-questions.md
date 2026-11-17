# Section B — Technical Short Questions (30 marks) — Ranked Model Answers

**Rank:** ★★★ = almost certain topic · ★★ = likely · ★ = possible.
Exam-e proti question ~5 marks, 5–8 line. **Answer format:** *Definition → How it works → Small example → Trade-off / common trap.*
Answer English-e likho (technical term English-e); neeche 1-line "Marks tip" dekho.

| # | Topic | Rank |
|---|---|---|
| 1 | Spring Boot request lifecycle | ★★★ |
| 2 | IoC and Dependency Injection | ★★★ |
| 3 | `@Transactional` — proxy, propagation, rollback, isolation | ★★★ |
| 4 | N+1 problem, lazy vs eager | ★★★ |
| 5 | PUT vs PATCH, idempotency | ★★★ |
| 6 | OAuth 2.0 Authorization Code + PKCE | ★★★ |
| 7 | JWT structure and validation | ★★★ |
| 8 | Authentication vs Authorization, RBAC | ★★★ |
| 9 | ACID and isolation levels | ★★★ |
| 10 | Database indexes | ★★★ |
| 11 | SQL injection, XSS, CSRF | ★★★ |
| 12 | REST API design best practices | ★★★ |
| 13 | Multi-tenant data isolation | ★★★ |
| 14 | SOLID principles | ★★★ |
| 15 | equals/hashCode and HashMap internals | ★★★ |
| 16 | Streams, lambdas, Optional | ★★ |
| 17 | Java concurrency basics | ★★ |
| 18 | JVM memory and garbage collection | ★★ |
| 19 | Spring Boot auto-configuration | ★★ |
| 20 | Global exception handling and validation | ★★ |
| 21 | DTO vs Entity | ★★ |
| 22 | Optimistic vs pessimistic locking | ★★ |
| 23 | JavaScript closure and event loop | ★★ |
| 24 | React state, hooks, performance | ★★ |
| 25 | Docker (image, container, volume, multi-stage) | ★★ |
| 26 | CI/CD and deployment strategies | ★★ |
| 27 | Observability | ★★ |
| 28 | Circuit breaker, retry, timeout | ★★ |
| 29 | Normalization vs denormalization | ★★ |
| 30 | Caching (Redis) and invalidation | ★★ |
| 31 | Monolith vs microservices | ★ |
| 32 | Offset vs keyset pagination | ★ |
| 33 | Backup, RPO, RTO | ★ |
| 34 | Comparable vs Comparator; interface vs abstract class | ★ |

---

## 1 ★★★ Explain the Spring Boot request lifecycle.
A request arrives at the embedded server (Tomcat) → passes the servlet **filter chain** (e.g., Spring Security: authentication/authorization, CORS) → reaches `DispatcherServlet` → **HandlerMapping** finds the controller method → arguments are bound (`@PathVariable`, `@RequestBody` via Jackson, `@Valid` validation) → **Controller** calls **Service** (business logic, `@Transactional`) → **Repository** (Spring Data JPA/Hibernate) talks to the database → the return value is serialized to JSON by `HttpMessageConverter` → response goes back through the filters. Exceptions are handled by `@RestControllerAdvice`.
*Marks tip:* draw the 5-box chain Client → Filter → Controller → Service → Repository → DB.

## 2 ★★★ What are IoC and Dependency Injection?
**IoC (Inversion of Control):** the framework, not the class, creates and wires objects. The Spring container (ApplicationContext) instantiates beans, injects their dependencies and manages their lifecycle. **DI** is how this is done: constructor, setter or field injection. **Constructor injection is preferred** — dependencies are explicit, can be `final` (immutability), and are easy to mock in unit tests. Benefit: loose coupling, testability. Default bean scope is **singleton**.

## 3 ★★★ How does `@Transactional` work? Explain propagation, rollback and isolation.
Spring wraps the bean in a **proxy**; the proxy begins a transaction before the method and commits after, or rolls back on exception. Defaults: rollback on **unchecked** (`RuntimeException`, `Error`) only — use `rollbackFor = Exception.class` for checked ones. **Propagation:** `REQUIRED` (default: join or create), `REQUIRES_NEW` (suspend current, start new), `SUPPORTS`, `MANDATORY`, `NEVER`, `NESTED`. **Isolation:** `READ_COMMITTED`, `REPEATABLE_READ`, `SERIALIZABLE`.
*Traps:* **self-invocation** (calling a `@Transactional` method from the same class) bypasses the proxy; it works only on **public** methods through the proxy; keep transactions short; `readOnly = true` for queries.

## 4 ★★★ What is the N+1 problem? Lazy vs eager loading?
Loading N parent rows then issuing **one extra query per parent** to load a lazy relation: 1 + N queries. **Lazy** loads the association on first access (default for `@OneToMany`; may throw `LazyInitializationException` outside the session); **eager** loads it immediately (default for `@ManyToOne`; can over-fetch). **Fix:** `JOIN FETCH` in JPQL, `@EntityGraph`, batch fetching (`@BatchSize`), or a DTO projection. Detect with SQL logging or Hibernate statistics.

## 5 ★★★ PUT vs PATCH; what is idempotency?
**PUT** replaces the whole resource (client sends the full representation); **PATCH** applies a partial update. **Idempotent** = repeating the same request gives the same server state: GET, PUT, DELETE, HEAD are idempotent; POST is not; PATCH may or may not be. For safe retries of POST (payments, submissions) use an **Idempotency-Key** header stored server-side. Example: `PUT /employees/5` with full body vs `PATCH /employees/5` with `{"salary": 60000}`.

## 6 ★★★ Explain OAuth 2.0 Authorization Code flow with PKCE. Why is PKCE needed?
1. Client generates a random `code_verifier` and its hash `code_challenge` (S256).
2. Client redirects the user to the Authorization Server with `response_type=code`, `client_id`, `redirect_uri`, `scope`, `state`, `code_challenge`.
3. User authenticates and consents.
4. Authorization Server redirects back with a one-time `code` (+ `state`, which the client verifies).
5. Client calls the token endpoint with `code` + the original `code_verifier`.
6. Server hashes the verifier, compares with the stored challenge, then issues access (and refresh/ID) tokens.
7. Client calls the API with `Authorization: Bearer <access_token>`.

**Why PKCE:** public clients (SPA, mobile) cannot keep a client secret; PKCE stops a stolen authorization code from being redeemed by an attacker (code-interception attack). Implicit and Password flows are discouraged.

## 7 ★★★ What is a JWT? How do you validate it?
JWT = `header.payload.signature`, each Base64URL-encoded. Header: algorithm/`kid`; payload: claims (`iss`, `sub`, `aud`, `exp`, `iat`, roles); signature: HMAC or RSA/ECDSA. **A JWT is signed, not encrypted — never put secrets in the payload.** Validation: verify the signature with the trusted key (JWKS), reject `alg: none`, check `exp`/`nbf`, `iss`, `aud`, then apply authorization. Keep access tokens short-lived; use refresh tokens with rotation; revocation needs a deny-list or short expiry.

## 8 ★★★ Authentication vs Authorization; explain RBAC.
**Authentication** = who are you (login, password, MFA, token). **Authorization** = what are you allowed to do. Failure codes: **401** (not authenticated), **403** (authenticated but forbidden). **RBAC:** permissions are assigned to roles, roles to users (e.g., `ADMIN`, `PO_USER`, `PKSF_ANALYST`). Spring: `@PreAuthorize("hasRole('ADMIN')")`. Enforce on the **server** every time, and combine with **data-scope** rules (user sees only their organization's rows). Principle of least privilege.

## 9 ★★★ Explain ACID and isolation levels.
**Atomicity** (all or nothing), **Consistency** (constraints/invariants preserved), **Isolation** (concurrent transactions don't see each other's partial work), **Durability** (committed data survives crashes, via WAL). Isolation levels and anomalies: *Read Uncommitted* (dirty read) → *Read Committed* (no dirty read; non-repeatable read possible) → *Repeatable Read* (no non-repeatable read; phantom possible in the standard) → *Serializable* (no anomalies). **PostgreSQL default = Read Committed** (it implements MVCC; its Read Uncommitted behaves as Read Committed).

## 10 ★★★ What is a database index? When should you (not) use one?
An index is a separate data structure (default **B-tree**) that lets the database find rows without scanning the whole table: faster `WHERE`, `JOIN`, `ORDER BY`; slower writes and more storage. Types: B-tree, Hash, **GIN** (JSONB, arrays, full-text), GiST, BRIN. **Composite index** `(a,b)` follows the leftmost-prefix rule. Not used when: function on the column (`LOWER(col)`), leading wildcard `LIKE '%x'`, low selectivity, type mismatch, tiny tables. Always check with `EXPLAIN ANALYZE`.

## 11 ★★★ How do you prevent SQL injection, XSS and CSRF?
- **SQL injection:** parameterized queries / `PreparedStatement` / JPA named parameters; never concatenate input; least-privilege DB user; validate input.
- **XSS:** output encoding/escaping (React escapes by default; avoid `dangerouslySetInnerHTML`), Content-Security-Policy, `HttpOnly` cookies, sanitize HTML.
- **CSRF:** attack on cookie-based sessions; defend with CSRF tokens, `SameSite` cookies, checking `Origin`. Token-in-header APIs (Bearer) are not CSRF-prone by default.
Also: HTTPS/TLS, secure headers, dependency scanning (OWASP Top 10).

## 12 ★★★ REST API design best practices?
Noun-based plural resources (`/api/v1/employees/{id}`); correct HTTP verbs; correct status codes (200, **201** + `Location`, **204**, 400, 401, 403, 404, **409**, 422, **429**, 500, 503); JSON error body with code/message/traceId; **pagination** (`page`,`size` or cursor), filtering, sorting; **versioning** (`/v1`); idempotency for retries; validation; HATEOAS optional; OpenAPI docs; rate limiting; HTTPS; authentication on every endpoint; don't expose entities (use DTOs).

## 13 ★★★ How do you isolate data in a multi-tenant system?
Models: **(a)** shared DB + shared schema with `organization_id` on every row (cheapest, most common), **(b)** schema per tenant, **(c)** database per tenant (strongest isolation, highest cost). For (a): derive tenant from the **authenticated identity/membership on the server — never trust a `tenant_id` from the request body**; apply the filter in every query path (service/repository, Hibernate filter) and add **PostgreSQL Row-Level Security** as defense-in-depth; tenant-scope cache keys, exports, background jobs and file paths; cross-tenant negative tests; audit privileged central access.

## 14 ★★★ Explain SOLID.
**S**ingle Responsibility — one reason to change. **O**pen/Closed — open for extension, closed for modification (add new strategy, don't edit if-else). **L**iskov Substitution — subtypes must be usable wherever the base type is expected without breaking behavior. **I**nterface Segregation — many small interfaces, not one fat one. **D**ependency Inversion — depend on abstractions, not concretions (Spring DI). Example: `PaymentProcessor` interface with `BkashProcessor`, `CardProcessor`.

## 15 ★★★ Why override both `equals()` and `hashCode()`? How does HashMap work?
Contract: if `a.equals(b)` then `a.hashCode() == b.hashCode()`. Breaking it makes hash-based collections lose objects. **HashMap:** array of buckets; `hash(key)` selects a bucket; collisions are stored as a linked list, converted to a **red-black tree** when a bucket gets long (≥8 entries, Java 8+); `get` = hashCode → bucket → `equals`. Average O(1). Default load factor 0.75, then resize (double). One `null` key allowed (HashMap), not in `ConcurrentHashMap`. Use immutable keys.

## 16 ★★ Streams, lambdas and Optional.
A **lambda** is an anonymous implementation of a functional interface (`Predicate`, `Function`, `Consumer`, `Supplier`). A **Stream** is a pipeline: source → *intermediate* ops (lazy: `filter`, `map`, `sorted`, `distinct`) → *terminal* op (`collect`, `forEach`, `count`, `reduce`) that triggers execution. `map` transforms 1:1, `flatMap` flattens nested collections. Streams don't modify the source. `Optional<T>` represents a possibly-absent value: use `orElse`, `orElseThrow`, `map`; don't use it for fields/parameters. Parallel streams only for CPU-heavy, stateless work.

## 17 ★★ Java concurrency basics.
`synchronized` gives mutual exclusion and visibility; `volatile` gives visibility/ordering but **not atomicity** (`count++` is unsafe); `AtomicInteger` gives lock-free atomic ops (CAS). Prefer `ExecutorService` thread pools over raw threads; `CompletableFuture` for async composition; `ConcurrentHashMap` for shared maps. **Race condition** = outcome depends on timing; **deadlock** = threads wait for each other's locks — avoid by consistent lock ordering, timeouts (`tryLock`), and minimal lock scope.

## 18 ★★ JVM memory and garbage collection.
JVM = runs bytecode; JRE = JVM + libraries; JDK = JRE + compiler/tools. Memory: **Heap** (objects; Young gen → Old gen), **Metaspace** (class metadata), **Stack** (per thread: frames, locals), PC register. **GC** reclaims unreachable objects; modern collectors: **G1** (default), ZGC/Shenandoah (low pause). Memory leaks in Java = lingering references (static collections, listeners, caches). Tune with `-Xms/-Xmx`, monitor GC logs.

## 19 ★★ What is Spring Boot auto-configuration?
`@SpringBootApplication` = `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan`. Auto-configuration inspects the classpath and properties and registers beans conditionally (`@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`) — e.g., `spring-boot-starter-web` configures Tomcat and Jackson; a JDBC driver + `spring.datasource.*` configures `DataSource`. You override by defining your own bean or properties. Starters bundle compatible dependencies.

## 20 ★★ How do you handle validation and exceptions globally in Spring?
Annotate DTOs with Bean Validation (`@NotBlank`, `@Size`, `@Email`, `@Positive`) and use `@Valid` on `@RequestBody`; failures raise `MethodArgumentNotValidException`. Create a `@RestControllerAdvice` with `@ExceptionHandler` methods mapping exceptions to consistent JSON errors and status codes (400/404/409/500) with a correlation ID; never leak stack traces; log server-side.

## 21 ★★ Why use DTOs instead of exposing entities?
Entities map to the DB schema and may contain sensitive/internal fields, lazy relations (serialization errors, N+1), and bidirectional cycles. DTOs define a stable API contract, enable per-endpoint shape, versioning, validation, and prevent **mass assignment** (client setting `role` or `organizationId`). Map with MapStruct/manual mappers.

## 22 ★★ Optimistic vs pessimistic locking.
**Optimistic:** assume conflicts are rare; a `@Version` column is checked on update — if changed, `OptimisticLockException` and retry. Best for web apps with low contention. **Pessimistic:** lock rows up front (`SELECT ... FOR UPDATE`, `LockModeType.PESSIMISTIC_WRITE`) — safe for high contention (e.g., stock/balance), but reduces concurrency and risks deadlocks.

## 23 ★★ Explain JavaScript closure and the event loop.
A **closure** is a function plus the lexical scope it was created in, so it can access outer variables after the outer function returns (used for private state, callbacks, memoization). The **event loop**: JS is single-threaded; synchronous code runs on the call stack; async callbacks wait in queues; after the stack empties, **microtasks** (Promise `.then`, `queueMicrotask`) run before **macrotasks** (`setTimeout`, I/O, events). So `Promise.resolve().then(...)` runs before `setTimeout(..., 0)`. `async/await` is syntax over Promises; `Promise.all` rejects if any rejects (`allSettled` waits for all).

## 24 ★★ React: state, hooks, performance.
**Props** are read-only inputs; **state** is component-owned data (`useState`); updates are immutable and trigger re-render. `useEffect(fn, deps)` runs after render for side effects (API calls, subscriptions) — empty `[]` = once on mount; return a cleanup function; wrong deps cause stale data or loops. `useMemo`/`useCallback` memoize values/functions, `React.memo` skips re-render when props are unchanged; stable `key` in lists; lift state up/Context for sharing; code-splitting with `lazy`. Load pattern: `loading / error / data` states.

## 25 ★★ Docker: image, container, volume, multi-stage build.
**Image** = immutable layered template; **container** = running instance; **volume** = persistent data outside the container filesystem; **Dockerfile** builds the image; **Compose** runs multi-container apps locally. **Multi-stage build:** build in a JDK/Maven stage, copy only the jar into a small JRE runtime image → smaller, safer image. Run as non-root, pin versions, use `.dockerignore`, keep secrets out of images.

## 26 ★★ CI/CD and deployment strategies.
**CI:** every commit triggers build, unit/integration tests, static analysis, dependency scan. **CD:** automated delivery/deployment of the artifact through environments. Strategies: **rolling** (replace instances gradually), **blue-green** (two environments, switch traffic, instant rollback, double cost), **canary** (small % of traffic first). Add health/readiness checks, database migrations (Flyway/Liquibase, backward-compatible), feature flags, rollback plan.

## 27 ★★ What is observability?
Ability to understand system state from outputs: **logs** (structured, with correlation ID), **metrics** (latency, error rate, traffic, saturation — RED/USE/golden signals), **traces** (request across services, OpenTelemetry). Plus dashboards and actionable alerts (SLO-based). Never log passwords/tokens/PII. Spring Boot Actuator exposes `/health`, `/metrics`; Prometheus + Grafana is a common stack.

## 28 ★★ Circuit breaker, retry and timeout.
**Timeout** — never wait forever on a dependency. **Retry** — only for transient errors and idempotent operations, with **exponential backoff + jitter** and a max attempts cap (avoids retry storms). **Circuit breaker** — after a failure threshold it *opens* and fails fast, then *half-open* probes recovery (Resilience4j). Also bulkhead, fallback, rate limiting, dead-letter queue.

## 29 ★★ Normalization vs denormalization.
Normalization (1NF: atomic values; 2NF: no partial dependency; 3NF: no transitive dependency) removes redundancy and update/insert/delete anomalies. Denormalization (duplicated/pre-aggregated data, materialized views) speeds reads for reporting at the cost of write complexity and consistency risk. OLTP → normalize; reporting/analytics → selective denormalization.

## 30 ★★ Caching with Redis; invalidation.
Redis = in-memory key-value store used for caching, sessions, rate limiting, queues. Patterns: **cache-aside** (read cache → miss → DB → populate), write-through/write-behind. Set **TTL**; invalidate on update; beware stale data, cache stampede (locking/jitter), memory limits (eviction policy). Multi-tenant: include tenant ID in the key. Cache only measured hot data.

## 31 ★ Monolith vs microservices.
**Monolith** (modular): one deployable, simple transactions/debugging/deployment, scaling is all-or-nothing. **Microservices:** independent deploy/scale/ownership, tech flexibility, but distributed-system costs — network failures, eventual consistency, observability, ops overhead. Start with a **modular monolith**; split along stable domain boundaries when there is a real scaling/team reason.

## 32 ★ Offset vs keyset pagination.
`OFFSET n LIMIT k` is simple, supports page jump, but gets slower with large offsets and can skip/duplicate rows when data changes. **Keyset** (`WHERE (created_at, id) > (?, ?) ORDER BY created_at, id LIMIT k`) uses an index, stays fast and stable; no random page jump. Use keyset for large/infinite-scroll lists, offset for small admin tables.

## 33 ★ Backup, RPO and RTO.
**RPO** (Recovery Point Objective) = maximum acceptable data loss (time); **RTO** (Recovery Time Objective) = maximum acceptable downtime. PostgreSQL: periodic base backups + WAL archiving → **point-in-time recovery**; replicas for HA; store backups off-site/encrypted; **test restores regularly** — an untested backup is not a backup.

## 34 ★ Comparable vs Comparator; interface vs abstract class.
`Comparable<T>` defines the **natural order** inside the class (`compareTo`); `Comparator<T>` is an **external** strategy (`compare`), allows multiple orderings (`Comparator.comparing(Emp::salary).thenComparing(...)`). **Interface**: contract, multiple inheritance of type, default methods; **abstract class**: shared state/partial implementation, single inheritance, constructors. Prefer composition over inheritance.
