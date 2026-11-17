# PKSF Senior Software Engineer (Full-stack) — Written Mock Test

**পরীক্ষার তারিখ:** ২১ নভেম্বর ২০২৬  
**সময়:** ২ ঘণ্টা  
**পূর্ণমান:** ১০০  
**পদ:** Senior Software Engineer (Full-stack)

> **Disclaimer:** এটি প্রস্তুতির জন্য তৈরি অনুশীলনী মডেল; PKSF-এর অফিসিয়াল প্রশ্নপত্র বা নিশ্চিত সিলেবাস নয়। বিজ্ঞপ্তির প্রযুক্তিগত বিষয় ও Senior Full-stack পদের সাধারণ দক্ষতা বিবেচনায় প্রশ্ন সাজানো হয়েছে।

## পরীক্ষার কাঠামো

| Section | বিষয় | নম্বর |
|---|---|---:|
| A | MCQ & Fundamentals | 20 |
| B | Technical Short Questions | 30 |
| C | SQL & Coding | 25 |
| D | System Design & Architecture | 15 |
| E | Problem Solving & English | 10 |
| **মোট** | | **100** |

## Section A — MCQ (২০ × ১ = ২০)

প্রতিটি প্রশ্নের সঠিক উত্তর নির্বাচন করুন।

1. Which OOP concept bundles data and methods and controls access to internal state?  
   A. Inheritance · B. Encapsulation · C. Polymorphism · D. Abstraction
2. Which Spring annotation can be used for dependency injection?  
   A. @Entity · B. @Autowired · C. @Table · D. @Override
3. Which HTTP method is commonly used for a partial resource update?  
   A. GET · B. POST · C. PUT · D. PATCH
4. Which status code normally indicates successful resource creation?  
   A. 200 · B. 201 · C. 204 · D. 301
5. What is PostgreSQL's default index access method?  
   A. Hash · B. GIN · C. B-tree · D. BRIN
6. ACID stands for:  
   A. Atomicity, Consistency, Isolation, Durability · B. Accuracy, Consistency, Integrity, Data · C. Atomicity, Concurrency, Index, Durability · D. None
7. Which OAuth 2.0 flow is recommended for public clients such as SPAs and native apps?  
   A. Implicit · B. Resource Owner Password · C. Authorization Code with PKCE · D. Basic Authentication
8. What is a JWT primarily used for?  
   A. Database indexing · B. Compact claims/token representation · C. File compression · D. Server monitoring
9. Which Spring annotation combines controller semantics with response-body serialization by default?  
   A. @ControllerAdvice · B. @RestController · C. @Repository · D. @Configuration
10. Which HTTP status usually indicates missing or invalid authentication credentials?  
    A. 400 · B. 401 · C. 403 · D. 404
11. Which SQL keyword removes duplicate rows from a query result?  
    A. UNIQUE · B. DISTINCT · C. GROUP · D. REMOVE
12. Which SQL feature supports recursive queries in PostgreSQL?  
    A. WITH RECURSIVE · B. DROP · C. TRUNCATE · D. VACUUM
13. A primary goal of normalization is to:  
    A. Increase duplication · B. Reduce redundancy and update anomalies · C. Increase memory · D. Improve CSS
14. Which Java collection abstraction does not permit duplicate elements?  
    A. List · B. ArrayList · C. Set · D. LinkedList
15. Dependency Injection primarily promotes:  
    A. Tight coupling · B. Loose coupling · C. Data duplication · D. Obfuscation
16. Which React hook manages local component state?  
    A. useEffect · B. useState · C. useMemo · D. useRef
17. CORS means:  
    A. Cross-Origin Resource Sharing · B. Central Object Request System · C. Common Origin Response Service · D. Cross-Origin Routing System
18. Redis is commonly used for:  
    A. Relational schema design · B. In-memory data storage and caching · C. Frontend rendering · D. Source control
19. Which Docker artifact is a packaged, immutable template used to create containers?  
    A. Container · B. Image · C. Volume · D. Network
20. A database transaction is used to:  
    A. Execute a group of operations reliably · B. Improve CSS · C. Compile Java · D. Encrypt an endpoint

### MCQ Answer Key

| প্রশ্ন | উত্তর | সংক্ষিপ্ত কারণ |
|---|---|---|
| 1 | B | Encapsulation internal state ও access নিয়ন্ত্রণ করে |
| 2 | B | @Autowired injection করতে পারে; constructor injection সাধারণত পছন্দনীয় |
| 3 | D | PATCH partial modification-এর জন্য |
| 4 | B | 201 Created |
| 5 | C | B-tree default index method |
| 6 | A | ACID-এর পূর্ণরূপ |
| 7 | C | Public client-এর জন্য Authorization Code + PKCE |
| 8 | B | JWT claims বহনকারী compact token format |
| 9 | B | @RestController = @Controller + @ResponseBody |
| 10 | B | 401 authentication অনুপস্থিত/অকার্যকর |
| 11 | B | DISTINCT duplicate result row বাদ দেয় |
| 12 | A | WITH RECURSIVE |
| 13 | B | Redundancy ও update anomaly কমায় |
| 14 | C | Set duplicate রাখে না |
| 15 | B | Loose coupling |
| 16 | B | useState |
| 17 | A | Cross-Origin Resource Sharing |
| 18 | B | In-memory data store/cache |
| 19 | B | Docker Image |
| 20 | A | Transaction atomic ও নির্ভরযোগ্য operation group |

## Section B — Technical Short Questions (৬ × ৫ = ৩০)

প্রতিটি প্রশ্নের উত্তর ৫–৮ লাইনে লেখার চেষ্টা করুন।

21. Explain the four pillars of Object-Oriented Programming with examples.
22. Explain the Spring Boot request lifecycle. How does a request reach Controller, Service and Repository?
23. What is the difference between PUT and PATCH? Give REST API examples.
24. Explain ACID properties in PostgreSQL with a practical example.
25. Explain OAuth 2.0 Authorization Code Flow with PKCE. Why is PKCE important?
26. Differentiate Authentication and Authorization. Explain Role-Based Access Control (RBAC).

### Model Answers — Section B

**21. Four pillars of OOP**

- **Encapsulation:** Data ও method-কে class-এর মধ্যে রাখা এবং access modifier দিয়ে internal state নিয়ন্ত্রণ করা।
- **Inheritance:** একটি class অন্য class-এর property ও behaviour inherit করতে পারে।
- **Polymorphism:** একই interface বা method call ভিন্ন implementation-এ ভিন্ন আচরণ করতে পারে।
- **Abstraction:** প্রয়োজনীয় interface প্রকাশ করে implementation detail আড়াল করা।

উদাহরণ:

```java
abstract class Animal {
    abstract void sound();
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

**22. Spring Boot request lifecycle**

সাধারণ flow: Client → Servlet Filter/Security Filter Chain → DispatcherServlet → HandlerMapping → Controller → Service → Repository → Database। Repository-এর result Service ও Controller হয়ে response হিসেবে ফেরত যায়। DispatcherServlet request routing করে; Controller HTTP input/output সামলায়; Service business logic ও transaction boundary রাখে; Repository persistence পরিচালনা করে। Exception সাধারণত @ControllerAdvice দিয়ে centrally handle করা যায়।

**23. PUT vs PATCH**

- PUT সাধারণত resource-এর সম্পূর্ণ representation replace করে; একই request বারবার করলে একই final state হওয়া উচিত (idempotent)।
- PATCH resource-এর নির্দিষ্ট অংশ পরিবর্তন করে; PATCH operation সবসময় idempotent হবে এমন নিশ্চয়তা নেই—নির্দিষ্ট patch semantics-এর ওপর নির্ভর করে।

উদাহরণ:

```http
PUT /api/users/10
Content-Type: application/json

{"name":"Rahim","email":"rahim@example.com"}
```

```http
PATCH /api/users/10
Content-Type: application/merge-patch+json

{"name":"Hasan"}
```

**24. ACID properties**

- **Atomicity:** Transaction সম্পূর্ণ হবে, নতুবা rollback হবে।
- **Consistency:** Constraint ও business invariant বজায় থাকবে।
- **Isolation:** Concurrent transaction-এর পারস্পরিক প্রভাব নির্ধারিত isolation level অনুযায়ী নিয়ন্ত্রিত হবে।
- **Durability:** Commit-এর পর data স্থায়ী থাকবে।

উদাহরণ: একটি account থেকে ১,০০০ টাকা debit এবং অন্য account-এ credit—দুই operation একই transaction-এ করতে হবে; একটিতে ব্যর্থ হলে rollback করতে হবে।

**25. OAuth 2.0 Authorization Code with PKCE**

1. Client random code_verifier তৈরি করে এবং তার code_challenge হিসাব করে।
2. Browser-কে Authorization Server-এ পাঠায়; request-এ challenge থাকে।
3. User authentication ও consent সম্পন্ন করে।
4. Authorization Server redirect URI-তে authorization code ফেরত দেয়।
5. Client code ও original verifier দিয়ে token endpoint-এ request করে।
6. Server challenge/verifier মিলিয়ে token দেয়।
7. Client access token দিয়ে protected API call করে।

PKCE authorization code interception হলেও verifier ছাড়া token exchange ঠেকাতে সাহায্য করে। OAuth 2.0 হলো authorization framework; user sign-in/identity layer প্রয়োজন হলে OpenID Connect (OIDC) ব্যবহৃত হয়।

**26. Authentication vs Authorization**

- Authentication: ব্যবহারকারী কে তা যাচাই (যেমন login)।
- Authorization: যাচাইকৃত ব্যবহারকারী কোন resource/action access করতে পারবেন তা নির্ধারণ।
- RBAC-এ permission role-এর সঙ্গে যুক্ত থাকে; user role পায়।

উদাহরণ: PKSF Admin সব organization-এর অনুমোদিত রিপোর্ট দেখতে পারেন; Partner User শুধু নিজের organization-এর data submit ও view করতে পারেন। প্রতিটি request-এ server-side permission এবং tenant scope enforce করতে হবে।

## Section C — SQL & Programming (২৫)

### 27. Department-wise employee count and average salary (৫)

ধরা যাক:

**employees**

| id | name | department_id | salary |
|---:|---|---:|---:|
| 1 | Rahim | 1 | 50000 |
| 2 | Karim | 1 | 65000 |
| 3 | Sumon | 2 | 45000 |
| 4 | Hasan | 2 | 70000 |
| 5 | Jamil | 3 | 60000 |

**departments**

| id | department_name |
|---:|---|
| 1 | IT |
| 2 | HR |
| 3 | Finance |

প্রশ্ন: প্রতিটি department-এর নাম, employee count এবং average salary বের করুন।

**উত্তর:**

```sql
SELECT
    d.department_name,
    COUNT(e.id) AS employee_count,
    COALESCE(AVG(e.salary), 0) AS average_salary
FROM departments d
LEFT JOIN employees e
    ON e.department_id = d.id
GROUP BY d.id, d.department_name
ORDER BY d.id;
```

Expected result:

| Department | Employee count | Average salary |
|---|---:|---:|
| IT | 2 | 57500 |
| HR | 2 | 57500 |
| Finance | 1 | 60000 |

LEFT JOIN ব্যবহার করায় employee না থাকলেও department result-এ থাকবে।

### 28. Second highest distinct salary (৫)

**উত্তর:**

```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
OFFSET 1
LIMIT 1;
```

অথবা:

```sql
SELECT MAX(salary)
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

দুই query-ই দ্বিতীয় সর্বোচ্চ distinct salary দেয়; পর্যাপ্ত distinct salary না থাকলে NULL/no row পাওয়া যেতে পারে।

### 29. PostgreSQL query optimization (৫)

কমপক্ষে পাঁচটি পদ্ধতি ব্যাখ্যা করুন:

1. EXPLAIN (ANALYZE, BUFFERS) দিয়ে actual execution plan ও bottleneck দেখা।
2. WHERE/JOIN/ORDER BY pattern অনুযায়ী উপযুক্ত index তৈরি; অপ্রয়োজনীয় index এড়িয়ে চলা।
3. SELECT * বাদ দিয়ে প্রয়োজনীয় column নেওয়া।
4. JOIN condition, filter ও data type mismatch ঠিক করা।
5. বড় result-এর জন্য pagination বা keyset pagination ব্যবহার।
6. N+1 query সমস্যা batch fetch/join দিয়ে সমাধান।
7. Statistics হালনাগাদ রাখা; প্রয়োজনমতো ANALYZE ও VACUUM।
8. Connection pooling ও query timeout যথাযথভাবে কনফিগার করা।

উদাহরণ:

```sql
CREATE INDEX idx_employees_department
ON employees(department_id);
```

Index query workload অনুযায়ী দিতে হবে; প্রতিটি column-এ index দিলেই performance বাড়ে না।

### 30. Java 8 Stream API — duplicate elements (১০)

Input:

```java
int[] numbers = {10, 20, 10, 30, 20, 40, 50};
```

**উত্তর:**

```java
import java.util.*;
import java.util.stream.*;

public class Main {
    public static void main(String[] args) {
        int[] numbers = {10, 20, 10, 30, 20, 40, 50};

        Set<Integer> seen = new HashSet<>();

        List<Integer> duplicates =
            Arrays.stream(numbers)
                .filter(n -> !seen.add(n))
                .distinct()
                .boxed()
                .collect(Collectors.toList());

        System.out.println(duplicates);
    }
}
```

Output:

```text
[10, 20]
```

HashSet.add() নতুন element-এর ক্ষেত্রে true এবং আগে থাকলে false দেয়। filter duplicate occurrence রাখে; distinct প্রতিটি duplicate value একবার রাখে। সময় জটিলতা গড়ে O(n), অতিরিক্ত space O(n)।

## Section D — System Design & Architecture (১৫)

### 31. Centralized Decision Support System (DSS)

**Scenario**

- ২০০+ Partner Organization data submit করবে।
- PKSF Admin সব organization-এর অনুমোদিত report দেখতে পারবেন।
- Organization A যেন Organization B-এর data access করতে না পারে।
- লক্ষ লক্ষ record থাকবে।
- Security, availability ও performance নিশ্চিত করতে হবে।

**প্রস্তাবিত Architecture**

```text
Partner Portals / Admin Dashboard (React)
                  |
             HTTPS / WAF
                  |
        API Gateway / Nginx
                  |
       Spring Boot Application
       |          |           |
 Authentication  Business   Reporting
 / Authorization   Services   Services
       |          |           |
       +----------+-----------+
                  |
       PostgreSQL (Primary DB)
          |              |
       Redis         Read Replica
          |
   Cache / Session / Rate Limit

Background Jobs / Queue
          |
   ETL / Data Validation
          |
   Analytics & Reports

Centralized Logs / Metrics / Alerts
Backup / PITR / Disaster Recovery
```

**১. Backend:** Spring Boot-এ modular/layered architecture—Controller, Service, Repository, DTO, validation, centralized exception handling এবং প্রয়োজনমতো transaction boundary।

**২. Database:** organizations, users, roles, permissions, submissions, reports, audit_logs—এমন normalized schema। Tenant-owned business table-এ organization_id, foreign key, appropriate composite index ও constraints রাখতে হবে।

**৩. Multi-tenant isolation:**

- Login-এর পর server-side identity থেকে tenant context নির্ধারণ।
- Request body বা query parameter-এর organization_id অন্ধভাবে বিশ্বাস নয়।
- প্রতিটি read/write query-তে tenant scope enforce।
- PostgreSQL Row-Level Security (RLS) defense-in-depth হিসেবে ব্যবহার করা যেতে পারে; connection pooling-এ tenant context নিরাপদে set/reset করতে হবে।
- Admin access আলাদা permission ও audit trail দিয়ে নিয়ন্ত্রণ।
- Cache key, background job, export ও report generation-এও tenant identifier enforce।

**৪. Security:** OAuth 2.0/OIDC-based identity, short-lived access token, RBAC, HTTPS, input validation, parameterized SQL, secrets management, rate limiting, audit logging এবং dependency/security patching।

**৫. Performance:** EXPLAIN ANALYZE, workload-based indexes, pagination/keyset pagination, Redis caching, connection pooling, batch ingestion, asynchronous report generation, read replica এবং monitoring।

**৬. Reliability:** automated backup, point-in-time recovery (PITR), replication, off-site backup, tested restore procedure, disaster recovery plan, health checks ও alerting। Replication একা backup-এর বিকল্প নয়।

**Senior-level discussion points:** Modular monolith দিয়ে শুরু করে প্রয়োজন অনুযায়ী service split; synchronous API ও asynchronous processing-এর সঠিক নির্বাচন; idempotent ingestion; retry/backoff; data validation/reconciliation; auditability; tenant isolation test।

## Section E — Problem Solving & English (১০)

### 32. Logical reasoning (৩)

Series: 2, 6, 12, 20, 30, ?

Difference: +4, +6, +8, +10; পরবর্তী difference +12।

**উত্তর: 42**

### 33. Mathematics (৩)

মোট বাজেট = ৮,০০,০০০ টাকা। Backend ২৫%, Frontend ৩৫%; মোট ব্যয় ৬০%, অবশিষ্ট ৪০%।

```text
Remaining = 800,000 × 40 / 100
          = 320,000 টাকা
```

**উত্তর: ৩,২০,০০০ টাকা।**

### 34. English writing (৪)

**Topic: How to improve the security and performance of an enterprise-level web application.**

**Model answer (under 100 words):**

To improve the security and performance of an enterprise-level web application, a comprehensive approach is necessary. We should implement OAuth 2.0/OpenID Connect and role-based access control, enforce HTTPS, validate inputs, and use parameterized queries to prevent common vulnerabilities. Performance can be improved through database query optimization, suitable indexing, Redis caching, pagination, and connection pooling. Containerized deployment and automated CI/CD can improve consistency and release reliability. Centralized logging, monitoring, regular security audits, tested backups, and disaster recovery procedures are essential for maintaining a secure, scalable, and reliable platform.

---

## পরীক্ষার সময় বণ্টন (১২০ মিনিট)

| Section | সময় |
|---|---:|
| MCQ | ১৫ মিনিট |
| Short Questions | ৩০ মিনিট |
| SQL & Coding | ৩৫ মিনিট |
| System Design | ২৫ মিনিট |
| Problem Solving & English | ১০ মিনিট |
| Final Review | ৫ মিনিট |
| **মোট** | **১২০ মিনিট** |

## শেষ মুহূর্তের Revision Checklist

- [ ] Java OOP, Collections, Stream API, Exception Handling
- [ ] Spring Boot DI, Security, JPA/Hibernate, REST API
- [ ] PostgreSQL JOIN, CTE, Index, EXPLAIN ANALYZE, ACID
- [ ] HTTP methods/status codes, OAuth 2.0, OIDC, JWT
- [ ] React Hooks, Promise, Async/Await, Event Loop
- [ ] Docker, CI/CD, Linux, monitoring
- [ ] Multi-tenant architecture, RBAC, data isolation
- [ ] Java coding ও SQL হাতে লিখে practice
- [ ] ১২০ মিনিটে পুরো mock test শেষ করার অনুশীলন

**Target:** ৮০+/১০০ — এটি ব্যক্তিগত প্রস্তুতির লক্ষ্য, কোনো official cutoff নয়।

শুভকামনা। পরীক্ষার আগে প্রশ্নগুলো না দেখে একবার সময় ধরে সম্পূর্ণ mock test দিন, তারপর answer key মিলিয়ে দুর্বল অংশ revise করুন।
