# REST, Security, JavaScript, React & DevOps — Q&A

## HTTP and REST

### 1. GET, POST, PUT, PATCH, DELETE?
**Answer:** GET retrieves; POST submits/creates or triggers processing; PUT replaces a resource representation; PATCH partially modifies; DELETE removes. GET, PUT and DELETE are defined as idempotent; POST is not generally idempotent. PATCH idempotency depends on patch semantics.

### 2. Common HTTP status codes?
**Answer:** 200 OK, 201 Created, 202 Accepted, 204 No Content, 400 Bad Request, 401 unauthenticated, 403 authenticated but forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable Content, 429 Too Many Requests, 500 Internal Server Error, 503 Service Unavailable.

### 3. What makes a good REST API?
**Answer:** Resource-oriented URLs, correct methods/status codes, validation, consistent error schema, pagination/filter/sort, versioning strategy, authentication/authorization, idempotency for retryable operations, OpenAPI documentation and observability.

### 4. How should pagination be designed?
**Answer:** For small/moderate lists use limit/offset with maximum page size and deterministic ordering. For large/changing datasets use cursor/keyset pagination with stable sort keys. Return next cursor/metadata and validate user-controlled sort fields.

### 5. What is idempotency?
**Answer:** Repeating the same request has the same intended effect as one request. For submissions/payments, accept an idempotency key, store its result with a unique constraint and return the original result for safe retries.

### 6. CORS vs CSRF?
**Answer:** CORS controls whether browser JavaScript may read cross-origin responses. CSRF tricks a browser into sending an authenticated request using ambient credentials. CORS is not a CSRF defense; use SameSite cookies, CSRF tokens and origin checks as appropriate.

## Identity and application security

### 7. Authentication vs authorization?
**Answer:** Authentication establishes identity; authorization decides what that identity may do. Enforce authorization server-side on every protected action and data scope.

### 8. OAuth 2.0 vs OpenID Connect?
**Answer:** OAuth 2.0 is an authorization framework for delegated access. OpenID Connect adds an identity layer, including ID Tokens and standardized user authentication claims.

### 9. What is JWT?
**Answer:** JSON Web Token is a compact claims format, often signed (JWS) or encrypted (JWE). A signed JWT is not secret by default: its payload can be decoded. Validate signature, allowed algorithm, issuer, audience, expiry and relevant claims. Never trust client-supplied roles without validated claims and policy checks.

### 10. Access token vs refresh token?
**Answer:** Access tokens are short-lived credentials for APIs. Refresh tokens obtain new access tokens and need stronger protection, rotation/reuse detection where supported, secure storage, revocation and narrow scope.

### 11. How to prevent SQL injection?
**Answer:** Use parameterized queries/prepared statements or ORM parameter binding; never concatenate untrusted values into SQL. Allowlist dynamic identifiers such as sort column names. Apply least-privilege DB credentials.

### 12. XSS?
**Answer:** Cross-Site Scripting injects attacker-controlled script into a page. Prevent with context-aware output encoding, safe templating, sanitization for allowed HTML, Content Security Policy and avoiding unsafe DOM APIs.

### 13. CSRF?
**Answer:** Cross-Site Request Forgery causes a logged-in browser to send unwanted state-changing requests. Use CSRF tokens for cookie-authenticated flows, SameSite cookies, origin checks and avoid state changes via GET.

### 14. Password storage?
**Answer:** Use Argon2id, bcrypt or scrypt with unique salts and tuned work factors; never plaintext or fast general-purpose hashes such as plain SHA-256. Add rate limits and secure recovery.

### 15. What is RBAC?
**Answer:** Role-Based Access Control maps users to roles and roles to permissions. Check both action permission and resource ownership/tenant scope. Roles alone do not prevent insecure direct object reference.

## JavaScript

### 16. What is a closure?
**Answer:** A function retains access to lexical variables from its outer scope even after that outer function returns. Closures are useful for encapsulation and callbacks; be aware of captured mutable state and memory retention.

### 17. What is hoisting?
**Answer:** JavaScript declarations are processed before execution within their scope. Function declarations can be called before their textual position; var is hoisted and initialized undefined; let/const are hoisted but inaccessible in the temporal dead zone before initialization.

### 18. What is the event loop?
**Answer:** JavaScript runs synchronous code on a call stack. Async work is completed by host APIs; callbacks are queued. Promise reactions run as microtasks, generally before the next task/macrotask.

### 19. Promise vs async/await?
**Answer:** A Promise represents eventual completion/failure. async functions return Promises; await pauses that async function until settlement. Use try/catch and Promise.all for independent concurrent tasks.

### 20. == vs ===?
**Answer:** == performs coercion before comparison; === compares without type coercion. Prefer === except when coercion is intentional.

## React

### 21. Props vs state?
**Answer:** Props are inputs passed by a parent and are read-only to the child. State is component-managed data; updating state schedules a render. Do not mutate either directly.

### 22. useEffect?
**Answer:** useEffect synchronizes a component with external systems such as subscriptions, timers or network connections. Specify correct dependencies and cleanup. Do not use it for values that can be derived during render.

### 23. useMemo vs useCallback?
**Answer:** useMemo caches a computed value; useCallback caches a function identity. They are performance optimizations, not correctness tools; profile before adding them.

### 24. How to avoid unnecessary React renders?
**Answer:** Keep state local, split components, use stable keys, avoid redundant state, memoize measured hotspots, virtualize long lists and avoid effect-driven redundant state.

### 25. How to handle API loading/errors?
**Answer:** Model loading, success, empty and error states; cancel or ignore stale requests on unmount/parameter change; validate responses; show useful errors; never expose secrets in frontend code.

## Docker, CI/CD and operations

### 26. Image vs container?
**Answer:** Image is a packaged filesystem/configuration template; container is a running or stopped instance created from an image.

### 27. What is Docker Compose?
**Answer:** A declarative way to define and run multi-container applications, networks and volumes, useful for local development and integration environments.

### 28. What is CI/CD?
**Answer:** Continuous Integration builds and tests changes frequently. Continuous Delivery keeps software releasable; Continuous Deployment automatically releases qualifying changes. A pipeline commonly includes lint, tests, security scans, build, artifact publish, deploy and health verification.

### 29. What should a production deployment include?
**Answer:** Immutable versioned artifact, configuration/secrets externalized, health/readiness checks, rolling or blue-green deployment, rollback plan, database migration strategy, logs/metrics/traces and actionable alerts.

### 30. What is observability?
**Answer:** Logs explain events; metrics quantify system behaviour; traces show request flow across components. Use correlation IDs, structured logs, latency/error/saturation dashboards and alerts. Avoid logging credentials or sensitive personal data.
