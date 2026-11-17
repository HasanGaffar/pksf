# PKSF Senior Full-stack — Final Sprint Plan (17 → 21 Nov 2026)

**Exam:** Sat, 21 Nov 2026 · 2 ghonta · 100 marks
**Ekhon theke shomoy:** ~26 ghonta focused study (tonight 2h + 18/19/20 Nov 8h kore). Raat 11:30–12:00 er moddhe ghum, ghum kata jabe na.

> **Shotti kotha:** "95% pass guarantee" keu dite pare na, ami-o na. Actual paper, cut-off ar onno candidate der upor result depend kore. Ei repo-r shob question *unofficial*. Ei plan-er goal: **honest timed mock-e 85+/100** tulte pare emon preparation, ar exam hall-e marks haraner common karon gulo (time, adha-likha answer, bhul trap) bondho kora.

---

## 1. Repo analysis — ki ache, ki nei

| Jinish | Finding | Ki korte hobe |
|---|---|---|
| Section A "500 MCQ" | Asholey **~115 ta concept**, proti concept 5 bar reword kora. | 546 ta question porben na. Concept list ek bar master korun. |
| Section B "500 Q&A" | 575 question, kintu **115 ta unique answer** (Section A r same concept set). | Ekhoni 115 concept = A + B er 50 marks cover. |
| Section C | 25 SQL + 15 Java problem. Java code gulo compile kore dekha hoyeche (FizzBuzz ta shudhu fragment, baki thik). | Ei 40 ta-i hater-e likhe practice korun. |
| Section D | Mattro 10 ta design, choto. Main: multi-tenant Partner Organization DSS. | DSS design ta mukhosto/draw practice (15 marks). |
| Section E | 50 item (20 aptitude, 10 logic, 15 English, 5 writing). | 10 marks, shoja marks — 1.5h drill. |
| Mock test | **Shudhu 1 ta** (existing 4-day plan 2 ta mock bole, kintu 1 ta-i ache). | 20 Nov-e oi 1 ta full timed. 2nd ta banano dorkar (niche dekhun). |
| Old plan | 17 Nov block shesh prai; tai ei plan tar jaygay. | Eta follow korun. |

**Marks-wise priority (jekhane marks beshi + repo-r content shobcheye kaje lage):**
1. **B (30) + C (25) = 55 marks** → shobcheye beshi shomoy.
2. **D (15)** → ekta template + DSS diagram mukhosto.
3. **A (20)** → alada kore porar dorkar nei; B-r concept pora hole hoye jabe.
4. **E (10)** → kom shomoy, beshi return.

> Exam *written* — tai **kagoje hate likhe practice** korun (code, SQL, diagram). Screen-e dekhe "bujhlam" aar kagoje likha onek alada.

---

## 2. Tier list — kon topic age

**Tier 1 (must, ~65% marks):**
Spring IoC/DI, `@Transactional` (proxy, rollback, propagation, isolation), JPA (lazy/eager, N+1, persist vs merge, `@Version`) · SQL (JOIN, GROUP BY/HAVING, CTE, window functions, top-N per group, NULL, index, `EXPLAIN ANALYZE`, keyset pagination) · REST/HTTP (methods, idempotency, status codes, pagination) · OAuth2 + PKCE, OIDC, JWT, RBAC, CORS vs CSRF, XSS, SQLi · multi-tenant DSS design · Java coding (Two Sum, Kadane, parentheses, binary search, merge intervals, LRU, duplicates by Stream).

**Tier 2 (~25%):**
Java core (equals/hashCode, collections, exceptions, Stream, Optional, volatile/synchronized, ExecutorService/CompletableFuture, GC/JVM) · JS (closure, event loop, Promise, async/await, `==` vs `===`) · React (props/state, useEffect, keys, controlled input, memo) · Docker, CI/CD, observability, circuit breaker, retry+jitter.

**Tier 3 (~10%):** Section E aptitude/English, extras.

---

## 3. Shomoy-shuchi

### Tonight — Tue 17 Nov (21:30–23:30, 2h)
- 21:30–22:15 → `question-bank/java-spring-boot.md` (28 Q) poro, answer dhaka diye nijer moto bolo.
- 22:15–23:00 → Section B-te: IoC/DI, `@Transactional` proxy/propagation/isolation/rollback, N+1, lazy/eager, optimistic locking — kagoje 5-line answer likho.
- 23:00–23:30 → **Error log** file banao (3 kolam: topic · ki bhul korle · shothik answer). Ghum.

### Wed 18 Nov — Backend + Database (Tier 1)
| Shomoy | Kaj |
|---|---|
| 08:30–10:30 | Java core + concurrency (Section B), tarpor **Section C Java 26–35** kagoje likho (Two Sum, Kadane, parentheses, binary search, merge, first non-repeating). Prottek-e complexity + edge case. |
| 10:45–12:45 | **SQL 1–25** (age nijer likho, tarpor mil). Beshi focus: #7 Nth-highest, #8 top-3 per dept, #9 above-avg, #14 running total, #15 rank, #17 recursive CTE, #20 delete duplicates, #21 keyset, #22 index, #24 transaction, #25 slow query. |
| 14:30–16:30 | REST/HTTP (`rest-security-react-devops.md` 1–6 + Section B API Qs): status code table, PUT vs PATCH, idempotency key, pagination. |
| 16:45–18:45 | **Security**: OAuth2 vs OIDC, Auth Code + **PKCE** (7 step mukhosto), JWT validation, access vs refresh token, RBAC, CORS vs CSRF, XSS, SQLi, password hashing (bcrypt/argon2). "Secure API checklist" 1 page likho. |
| 20:00–21:00 | Section A theke 20 MCQ, 15 min timed + error log update. |

### Thu 19 Nov — Frontend, DevOps, Design (Tier 1 + 2)
| Shomoy | Kaj |
|---|---|
| 08:30–10:30 | JS + React: closure, hoisting, event loop (microtask vs macrotask), Promise.all, async/await, hooks, useEffect deps, keys, controlled inputs. Mock-er Section B-type answer 5 ta likho. |
| 10:45–12:45 | Docker (image/container/volume/multi-stage), CI/CD, blue-green vs rolling, health/readiness, observability, circuit breaker, retry+jitter. |
| 14:30–17:00 | **System design (15 marks)**: DSS architecture 3 bar nijer hate aanko (React → ingress/WAF → Spring Boot → PostgreSQL + queue + object storage + monitoring). Tenant isolation: `organization_id`, server-side scope, PostgreSQL RLS, cache key/exports/jobs scoped. Section D-r baki 9 design-er key-points. |
| 17:15–18:45 | **Section E** timed (aptitude 20, logic 10, English 15, writing 2) — 45 min, baki time mistake review. |
| 20:00–21:00 | Error log theke weak item gulo abar likho. |

### Fri 20 Nov — Full mock + fix (mock day)
| Shomoy | Kaj |
|---|---|
| 09:00–11:00 | `mock-tests/2026-11-21-senior-software-engineer-fullstack.md` — **ThikThik 120 minute**, kagoje, kono note chara. Allocation: A 20 · B 35 · C 40 · D 15 · E 10 min. |
| 11:15–13:00 | Nijer answer nijei mark koro (strict). Prottek bhul error log-e. |
| 14:30–17:00 | Shobcheye dubol section-ta niye kaj. Section ta r ekbar scratch theke likho. Mock < 75 hole ei pura bikel oi section-e. |
| 17:15–18:30 | Section C: 6 ta SQL (30 min timed) + 4 ta Java coding (40 min timed). |
| 19:30–20:30 | **1-page cheat sheet** (status codes, PKCE steps, isolation levels, window funcs, SOLID, DSS checklist) — shudhu pore dekha. |
| 20:30 | Admit card, ID, 2–3 ta pen, ghori, route/transport, exam centre jachai. **22:30-e ghum**. |

### Sat 21 Nov — Exam day
- Jaga, halka nasta, **notun kichu poro na**. Shudhu cheat sheet 20 min.
- Kendre 45 min age pouchao.

---

## 4. Exam hall strategy (marks bachabe)

**Shomoy bonton:** A 20 · B 35 · C 40 · D 15 · E 10 = 120 min. Prothome pura paper 2 min poro, jeta jano seta age.

**Section B (5 marks) answer template:** 1 line definition → kibhabe kaj kore (2–3 line) → choto example/code → ekta trade-off ba common trap. 5–8 line.

**Section C:** assumption likho → code/SQL → time/space complexity → edge case (null, empty, duplicate). Adha code-o likho, partial marks pabe.

**Section D (15):** Requirement → diagram (components + data flow) → data model → security/tenant isolation → reliability (queue, retry, idempotency, backup/RPO/RTO) → trade-off. Repo-r "Architecture trade-off answer template" (Section D #10) ta follow koro.

**Section E:** formula likhe calculation dekhao; English-e choto, shothik sentence.

**Last 5 min:** shob question-e kichu likha ache ki na dekho, blank rakho na.

---

## 5. Common trap gulo (ei gulo-te beshi bhul hoy)

- `==` vs `.equals()`; `equals()` override korle `hashCode()` o.
- `@Transactional`: **self-invocation-e kaj kore na**, default-e shudhu *unchecked* exception-e rollback.
- N+1 query → `JOIN FETCH` / `@EntityGraph` / batch fetch.
- **401** = authentication nai/invalid · **403** = authenticated kintu permission nai.
- PUT = full replace (idempotent), PATCH = partial. POST idempotent na → idempotency key.
- JWT **encrypted na, shudhu signed** (payload-e secret rakha jabe na); `exp`, `iss`, `aud`, signature validate koro.
- SPA/mobile-e **Authorization Code + PKCE** (Implicit/Password flow na).
- CORS ≠ CSRF. CORS browser-er read policy; CSRF cookie-based session attack.
- `LEFT JOIN` + `WHERE` right-table filter → INNER JOIN-er moto hoye jay (filter `ON`-e dao).
- `NULL = NULL` false; `IS NULL` use koro. `COUNT(*)` vs `COUNT(col)`.
- `ROW_NUMBER` vs `RANK` vs `DENSE_RANK`.
- Tenant ID client-er body theke trust kora jabe na; server-side membership theke nao.
- `useEffect` dependency array, `key` prop, state immutability; `Promise.all` ekta fail korle shob reject.

---

## 6. Progress checklist
- [ ] 17 Nov: Spring/JPA/Transaction + error log toiri
- [ ] 18 Nov: Java coding 10 ta · SQL 25 ta · REST · Security
- [ ] 19 Nov: JS/React · DevOps · DSS diagram 3 bar · Section E timed
- [ ] 20 Nov: Full 120-min mock · marking · weak fix · cheat sheet · logistics
- [ ] Mock score: ______ / 100 (target 85+)
- [ ] 22:30 aager ghum (20 Nov)
