# Section A — MCQ & Fundamentals (20 marks) — Ranked 60 MCQ

Repo-r purano bank-e ~115 ta unique concept chhilo, proti concept 5 bar reword kora. Ekhane tar **shobcheye exam-likely 60 ta**, rank kore, ekta kore shothik answer ar 1-line karon shoho.
**Rank:** ★★★ = almost certain · ★★ = likely · ★ = possible. Age ★★★ (1–25) 100% pora, tarpor ★★, tarpor ★.
Exam-e 20 ta MCQ-te ~20 min — prottek MCQ 1 min, atke gele skip.

## ★★★ Tier 1 (1–25)

1. Which annotation combines `@Controller` and `@ResponseBody`?
   A. `@Service` · B. `@Repository` · C. `@RestController` · D. `@Component`
   **Answer: C** — `@RestController` = `@Controller` + `@ResponseBody`.

2. A logged-in user calls an endpoint they are not permitted to use. Which status fits best?
   A. 404 · B. 403 · C. 401 · D. 400
   **Answer: B** — 401 = not authenticated; 403 = authenticated but forbidden.

3. Which status code normally means a resource was created?
   A. 201 · B. 302 · C. 204 · D. 200
   **Answer: A** — 201 Created (+ `Location` header).

4. Which HTTP method is meant for a partial update?
   A. GET · B. HEAD · C. PUT · D. PATCH
   **Answer: D** — PATCH is partial; PUT replaces the whole resource.

5. Which set contains only idempotent methods?
   A. GET, PUT, DELETE · B. GET, POST, PUT · C. POST only · D. POST, PATCH, DELETE
   **Answer: A** — POST is not idempotent.

6. What is PostgreSQL's default index type?
   A. Hash · B. B-tree · C. BRIN · D. GIN
   **Answer: B** — B-tree.

7. Which clause filters groups after aggregation?
   A. WHERE · B. LIMIT · C. HAVING · D. ORDER BY
   **Answer: C** — `WHERE` filters rows before grouping; `HAVING` after.

8. `LEFT JOIN` returns:
   A. All rows from the right table · B. Cartesian product · C. Only matching rows · D. All rows from the left table plus matches from the right
   **Answer: D** — unmatched right columns are NULL.

9. Which OAuth 2.0 flow is recommended for SPA/mobile (public) clients?
   A. Authorization Code with PKCE · B. Client Credentials · C. Password · D. Implicit
   **Answer: A** — PKCE protects the code without a client secret.

10. A JWT payload is:
    A. Stored only server-side · B. Always opaque · C. Base64URL-encoded and signed, not encrypted · D. Encrypted by default
    **Answer: C** — anyone can decode it; never put secrets in it.

11. Why is constructor injection preferred in Spring?
    A. It avoids beans · B. It disables proxies · C. Dependencies are explicit, can be `final`, easy to test · D. It is faster at runtime
    **Answer: C**.

12. By default `@Transactional` rolls back on:
    A. `RuntimeException` and `Error` · B. All exceptions · C. Only checked exceptions · D. Never
    **Answer: A** — use `rollbackFor` for checked exceptions.

13. The N+1 query problem means:
    A. 1 query for parents + N queries for their lazy children · B. N indexes · C. N queries fail · D. N transactions
    **Answer: A** — fix with `JOIN FETCH`/`@EntityGraph`.

14. The best defense against SQL injection is:
    A. Hiding error messages only · B. Using GET · C. Escaping quotes manually · D. Parameterized queries
    **Answer: D**.

15. In ACID, "I" stands for:
    A. Integrity · B. Indexing · C. Isolation · D. Idempotency
    **Answer: C** — Isolation.

16. The default isolation level in PostgreSQL is:
    A. Read Uncommitted · B. Read Committed · C. Serializable · D. Repeatable Read
    **Answer: B**.

17. In Docker, an image is:
    A. A running process · B. An immutable template used to create containers · C. A volume · D. A network
    **Answer: B**.

18. Which React hook manages component state?
    A. `useState` · B. `useRef` · C. `useMemo` · D. `useEffect`
    **Answer: A**.

19. In JavaScript, `===` differs from `==` because it:
    A. Ignores case · B. Is slower · C. Does no type coercion · D. Compares only references
    **Answer: C** — `0 == '0'` true, `0 === '0'` false.

20. The `equals()`/`hashCode()` contract says:
    A. `hashCode` must be random · B. Equal objects must have equal hash codes · C. They are unrelated · D. Equal hash codes mean equal objects
    **Answer: B**.

21. CORS is:
    A. A database feature · B. A server firewall · C. An encryption protocol · D. A browser-enforced cross-origin policy relaxed by server headers
    **Answer: D**.

22. Passwords should be stored using:
    A. Reversible AES only · B. Plain text · C. MD5 · D. A slow salted hash (bcrypt/Argon2)
    **Answer: D**.

23. The goal of normalization is to:
    A. Speed up all reads · B. Increase redundancy · C. Remove foreign keys · D. Reduce redundancy and update anomalies
    **Answer: D**.

24. A checked exception in Java:
    A. Must be caught or declared · B. Is an `Error` · C. Extends `RuntimeException` · D. Is never thrown
    **Answer: A** — e.g., `IOException`.

25. Which statement about `HashMap` is true?
    A. It allows one null key · B. It keeps insertion order · C. It is thread-safe · D. It sorts keys
    **Answer: A** — `ConcurrentHashMap` disallows nulls; `LinkedHashMap` keeps order; `TreeMap` sorts.

## ★★ Tier 2 (26–45)

26. The default scope of a Spring bean is:
    A. prototype · B. session · C. request · D. singleton
    **Answer: D**.

27. Which is true of interface vs abstract class in Java?
    A. A class can extend many abstract classes · B. Interfaces have constructors · C. A class can implement many interfaces · D. Abstract classes cannot have fields
    **Answer: C**.

28. In Java Streams, when does an intermediate operation like `filter` execute?
    A. Never · B. Only when a terminal operation is invoked · C. Immediately · D. At compile time
    **Answer: B** — streams are lazy.

29. `flatMap` is used to:
    A. Remove nulls · B. Flatten nested collections/streams into one · C. Count elements · D. Sort elements
    **Answer: B**.

30. `volatile` guarantees:
    A. Mutual exclusion · B. Atomicity of `count++` · C. Deadlock freedom · D. Visibility of writes across threads
    **Answer: D**.

31. A `LazyInitializationException` usually occurs when:
    A. The DB is down · B. A bean is a singleton · C. A column is NULL · D. A lazy association is accessed after the session/transaction closed
    **Answer: D**.

32. OpenID Connect adds what on top of OAuth 2.0?
    A. Encryption · B. A new database · C. Rate limiting · D. An identity layer (ID token) for authentication
    **Answer: D**.

33. A common CSRF defense is:
    A. CSRF tokens / `SameSite` cookies · B. Caching · C. Gzip · D. Longer passwords
    **Answer: A**.

34. A good XSS defense is:
    A. Output encoding + Content-Security-Policy · B. Hiding the server header · C. Disabling HTTPS · D. Using POST
    **Answer: A**.

35. Which function numbers rows within a partition with no ties?
    A. `RANK()` · B. `DENSE_RANK()` · C. `NTILE()` · D. `ROW_NUMBER()`
    **Answer: D**.

36. Values 100, 100, 90 — `RANK()` yields:
    A. 1,2,2 · B. 1,2,3 · C. 1,1,3 · D. 1,1,2
    **Answer: C** — `DENSE_RANK()` would give 1,1,2.

37. `EXPLAIN ANALYZE` in PostgreSQL:
    A. Creates an index · B. Executes the query and shows actual timings and rows · C. Vacuums the table · D. Only estimates the plan
    **Answer: B**.

38. With a composite index on `(a, b)`, which filter can use it efficiently?
    A. `WHERE a + 1 = 5` · B. `WHERE b = 5` only · C. `WHERE a = 5` · D. `WHERE LOWER(a) = 'x'`
    **Answer: C** — leftmost-prefix rule; functions on the column break index use.

39. In the JavaScript event loop, which runs first after the current code finishes?
    A. Network callbacks · B. Promise microtasks · C. `setTimeout` callbacks · D. UI rendering only
    **Answer: B**.

40. `useEffect(fn, [])` runs:
    A. Once after the first render (mount) · B. Before render · C. Never · D. On every render
    **Answer: A**.

41. The purpose of the `key` prop in React lists:
    A. Event binding · B. Encryption · C. Styling · D. Stable identity so React can diff/reorder correctly
    **Answer: D**.

42. `Promise.all([p1, p2])` when `p1` rejects:
    A. Returns undefined · B. Rejects immediately with that error · C. Ignores it · D. Waits for p2 then resolves
    **Answer: B** — use `Promise.allSettled` to wait for all.

43. A Docker volume is used to:
    A. Expose ports · B. Schedule jobs · C. Build images · D. Persist data outside the container's writable layer
    **Answer: D**.

44. CI vs CD: CI primarily means:
    A. Code inspection by hand only · B. Frequently merging code with automated build and tests · C. Customer interviews · D. Cloud infrastructure
    **Answer: B**.

45. A circuit breaker:
    A. Stops calling a failing dependency and fails fast · B. Encrypts traffic · C. Caches DB rows · D. Retries forever
    **Answer: A**.

## ★ Tier 3 (46–60)

46. HTTP 409 means:
    A. Unauthorized · B. Not modified · C. Gone · D. Conflict (e.g., duplicate or version mismatch)
    **Answer: D**.

47. HTTP 429 means:
    A. Created · B. Too many requests (rate limited) · C. Bad gateway · D. Not found
    **Answer: B**.

48. HTTP 204 means:
    A. Redirect · B. Success with no response body · C. Server error · D. Partial content
    **Answer: B**.

49. A GIN index is best for:
    A. Spatial sorting only · B. Primary keys · C. Simple integer equality · D. JSONB, arrays and full-text search
    **Answer: D**.

50. `@Version` in JPA supports:
    A. Optimistic locking · B. Sharding · C. Lazy loading · D. Caching
    **Answer: A**.

51. A Java `record` is:
    A. A thread · B. A concise immutable data carrier (Java 16+) · C. A mutable bean · D. An annotation
    **Answer: B**.

52. In blue-green deployment you:
    A. Update servers one at a time in place · B. Deploy only at night · C. Keep two environments and switch traffic between them · D. Send traffic to 1% of users
    **Answer: C**.

53. A Kubernetes readiness probe decides:
    A. Image size · B. Whether the pod receives traffic · C. Whether to restart the container · D. Memory limit
    **Answer: B** — liveness decides restart.

54. Exponential backoff with jitter is used to:
    A. Speed up retries · B. Encrypt retries · C. Space retries out and avoid synchronized retry storms · D. Skip failures
    **Answer: C**.

55. `TRUNCATE` vs `DELETE`:
    A. `DELETE` drops the table · B. `TRUNCATE` supports `WHERE` · C. `TRUNCATE` removes all rows quickly without row-by-row filtering · D. They are identical
    **Answer: C**.

56. `@RestControllerAdvice` is used for:
    A. Global exception handling across controllers · B. Database access · C. Static files · D. Scheduling
    **Answer: A**.

57. An Idempotency-Key header lets clients:
    A. Cache GET · B. Log in · C. Retry a POST safely without duplicating the effect · D. Encrypt the body
    **Answer: C**.

58. RPO measures:
    A. Maximum tolerable data loss (time) · B. Latency · C. Maximum tolerable downtime · D. Server cost
    **Answer: A** — RTO is downtime.

59. The best row-level safety net for multi-tenant data in PostgreSQL is:
    A. Client-supplied `tenant_id` · B. Disabling indexes · C. Row-Level Security policies plus server-side tenant filtering · D. A bigger server
    **Answer: C**.

60. The principle of least privilege means:
    A. Everyone is admin · B. Remove all logging · C. Give only the minimum permissions needed · D. Use one shared account
    **Answer: C**.

---

### Answer-key (self-check: 60 ta cover kore likhe neo)
1C 2B 3A 4D 5A 6B 7C 8D 9A 10C · 11C 12A 13A 14D 15C 16B 17B 18A 19C 20B · 21D 22D 23D 24A 25A 26D 27C 28B 29B 30D · 31D 32D 33A 34A 35D 36C 37B 38C 39B 40A · 41D 42B 43D 44B 45A 46D 47B 48B 49D 50A · 51B 52C 53B 54C 55C 56A 57C 58A 59C 60C

### Exam-e MCQ strategy
- Elimination: 2 ta option shohoje bad — baki duto theke concept diye choose.
- "Always/never/only" thakle shondehojonok.
- Doubt hole mark kore agiye jao; section-er sheshe back.
- Negative marking ache kina notice-e dekho; na thakle blank rakho na.
