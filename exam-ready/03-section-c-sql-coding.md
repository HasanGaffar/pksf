# Section C — SQL & Coding (25 marks) — Ranked, Exam-Ready

**Rank legend:** ★★★ = almost certain / classic · ★★ = likely · ★ = possible.
Sob SQL (jeta `-- PG-only` na) SQLite-e, ar sob Java code Java 21-e **chalie verify kora**. Kagoje likhar shomoy: *assumption → query/code → complexity → edge case*.

## Rank table

| Rank | SQL | Java |
|---|---|---|
| ★★★ | S1 Nth highest · S2 Top-N per dept · S3 Above dept average · S4 Dept count/avg (empty dept) · S5 Duplicates · S6 Delete duplicates · S7 Customers without orders | J1 Two Sum · J2 Valid parentheses · J3 Duplicates with Streams · J4 First non-repeating char · J5 Palindrome · J6 Kadane · J7 Binary search |
| ★★ | S8 Running total · S9 Self join manager · S10 Latest order per customer · S11 Recursive CTE · S12 Conditional aggregation · S13 Keyset pagination · S14 Upsert · S15 HAVING | J8 Merge sorted arrays · J9 Char frequency · J10 Second largest · J11 Anagram · J12 Merge intervals · J13 LRU cache · J14 Thread-safe counter · J15 Streams on employees |
| ★ | S16 Monthly report · S17 Index + EXPLAIN · S18 Transaction · S19 DISTINCT ON · S20 NULL handling | J16 Reverse string/words · J17 FizzBuzz · J18 Missing number · J19 Prime/Fibonacci · J20 Singleton · J21 Spring CRUD |

---

# PART 1 — SQL

Schema (SQLite/PostgreSQL both):

```sql
CREATE TABLE departments (id BIGINT PRIMARY KEY, name VARCHAR(120) NOT NULL);
CREATE TABLE employees (
  id BIGINT PRIMARY KEY, name VARCHAR(120) NOT NULL,
  department_id BIGINT REFERENCES departments(id),
  salary NUMERIC(12,2), manager_id BIGINT REFERENCES employees(id),
  hire_date DATE NOT NULL
);
CREATE TABLE customers (id BIGINT PRIMARY KEY, name VARCHAR(120) NOT NULL);
CREATE TABLE orders (
  id BIGINT PRIMARY KEY, customer_id BIGINT REFERENCES customers(id),
  amount NUMERIC(12,2) NOT NULL, order_date DATE NOT NULL
);
```

## S1 ★★★ Nth highest distinct salary (here N = 2)
```sql
SELECT DISTINCT salary
FROM (SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
      FROM employees WHERE salary IS NOT NULL) t
WHERE rnk = 2;
```
Alternative for 2nd highest only:
```sql
SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees);
```
*Why DENSE_RANK:* tie hole-o rank gap hoy na. `RANK` use korle gap hoy; `ROW_NUMBER` tie-ke alada rank dey.

## S2 ★★★ Top 3 salaries in each department (ties included)
```sql
SELECT department_id, name, salary
FROM (SELECT department_id, name, salary,
             DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
      FROM employees) t
WHERE rnk <= 3
ORDER BY department_id, salary DESC;
```

## S3 ★★★ Employees earning more than their department average
```sql
SELECT e.id, e.name, e.salary
FROM employees e
JOIN (SELECT department_id, AVG(salary) AS avg_sal FROM employees GROUP BY department_id) d
  ON d.department_id = e.department_id
WHERE e.salary > d.avg_sal;
```

## S4 ★★★ Employee count and average salary per department (include empty departments)
```sql
SELECT d.id, d.name, COUNT(e.id) AS emp_count, COALESCE(AVG(e.salary), 0) AS avg_salary
FROM departments d
LEFT JOIN employees e ON e.department_id = d.id
GROUP BY d.id, d.name;
```
*Trap:* `COUNT(*)` dile empty dept-e 1 ashbe; `COUNT(e.id)` dao. Right-table filter `WHERE`-e dile LEFT JOIN INNER hoye jay — `ON`-e dao.

## S5 ★★★ Find duplicate employee names
```sql
SELECT name, COUNT(*) AS cnt FROM employees GROUP BY name HAVING COUNT(*) > 1;
```

## S6 ★★★ Delete duplicates but keep the lowest id
```sql
DELETE FROM employees
WHERE id NOT IN (SELECT MIN(id) FROM employees GROUP BY name);
```
(Real project-e age `SELECT` diye check koro, transaction-er moddhe chalao.)

## S7 ★★★ Customers who never placed an order (anti-join)
```sql
SELECT c.* FROM customers c
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
```
Alternative: `LEFT JOIN orders o ON o.customer_id = c.id WHERE o.id IS NULL`. `NOT IN` NULL thakle bhul result dey — `NOT EXISTS` safer.

## S8 ★★ Running total of salary by hire date
```sql
SELECT id, name, hire_date, salary,
       SUM(salary) OVER (ORDER BY hire_date, id) AS running_total
FROM employees;
```

## S9 ★★ Employee with manager name (self join) and employees who earn more than their manager
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e LEFT JOIN employees m ON m.id = e.manager_id;
```
```sql
SELECT e.name FROM employees e JOIN employees m ON m.id = e.manager_id
WHERE e.salary > m.salary;
```

## S10 ★★ Latest order per customer
```sql
SELECT customer_id, id AS order_id, amount, order_date
FROM (SELECT o.*, ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC, id DESC) AS rn
      FROM orders o) t
WHERE rn = 1;
```

## S11 ★★ Organization hierarchy (recursive CTE)
```sql
WITH RECURSIVE org AS (
  SELECT id, name, manager_id, 1 AS lvl FROM employees WHERE manager_id IS NULL
  UNION ALL
  SELECT e.id, e.name, e.manager_id, o.lvl + 1
  FROM employees e JOIN org o ON e.manager_id = o.id
)
SELECT * FROM org ORDER BY lvl, id;
```

## S12 ★★ Conditional aggregation
```sql
SELECT department_id,
       SUM(CASE WHEN salary >= 50000 THEN 1 ELSE 0 END) AS high_paid,
       SUM(CASE WHEN salary <  50000 THEN 1 ELSE 0 END) AS other
FROM employees GROUP BY department_id;
```

## S13 ★★ Keyset pagination (better than OFFSET for big tables)
```sql
SELECT id, name, hire_date FROM employees
WHERE (hire_date, id) > ('2024-01-01', 10)
ORDER BY hire_date, id
LIMIT 20;
```
OFFSET-e N row skip korte hoy (slow, unstable); keyset index use kore, `(hire_date, id)` composite index dao.

## S14 ★★ Upsert
```sql
INSERT INTO departments (id, name) VALUES (1, 'Finance')
ON CONFLICT (id) DO UPDATE SET name = EXCLUDED.name;
```

## S15 ★★ Departments with more than 2 employees and total salary
```sql
SELECT department_id, COUNT(*) AS cnt, SUM(salary) AS total
FROM employees GROUP BY department_id HAVING COUNT(*) > 2;
```
`WHERE` row filter (aggregate-er age), `HAVING` group filter (aggregate-er pore).

## S16 ★ Monthly employee count
```sql
-- PG-only
SELECT date_trunc('month', hire_date) AS month, COUNT(*) FROM employees GROUP BY 1 ORDER BY 1;
```

## S17 ★ Create a useful composite index and read the plan
```sql
-- PG-only
CREATE INDEX idx_emp_dept_salary ON employees (department_id, salary DESC);
EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM employees WHERE department_id = 3 ORDER BY salary DESC LIMIT 10;
```
Dekho: `Index Scan` vs `Seq Scan`, estimated vs actual rows, execution time. Leftmost-prefix rule: `(a,b)` index `WHERE a=?` ba `a=? AND b=?`-e kaj kore, shudhu `b=?`-e na. Index function-e wrap korle (`WHERE LOWER(name)=...`) use hoy na (expression index lagbe).

## S18 ★ Safe money transfer (transaction)
```sql
-- PG-only
BEGIN;
SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;     -- lock row
UPDATE accounts SET balance = balance - 500 WHERE id = 1 AND balance >= 500;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;   -- error hole ROLLBACK
```

## S19 ★ Latest row per group (PostgreSQL shortcut)
```sql
-- PG-only
SELECT DISTINCT ON (customer_id) customer_id, id, amount, order_date
FROM orders ORDER BY customer_id, order_date DESC, id DESC;
```

## S20 ★ NULL handling
```sql
SELECT id, COALESCE(salary, 0) AS salary FROM employees WHERE manager_id IS NULL;
```
`NULL = NULL` false/unknown; `IS NULL` use koro. `AVG/SUM` NULL ignore kore; `COUNT(*)` NULL-shoho shob row gone.

---

# PART 2 — Java Coding

Sob code ei import dhore: `java.util.*`, `java.util.stream.*`, `java.util.concurrent.*`, `java.util.concurrent.atomic.*`.

## J1 ★★★ Two Sum — O(n) time, O(n) space
```java
static int[] twoSum(int[] a, int target) {
    Map<Integer, Integer> seen = new HashMap<>();
    for (int i = 0; i < a.length; i++) {
        Integer j = seen.get(target - a[i]);
        if (j != null) return new int[]{j, i};
        seen.put(a[i], i);
    }
    return new int[0];
}
```
Edge: empty array, no answer, duplicates (`[3,3]`, target 6).

## J2 ★★★ Valid parentheses — O(n), stack
```java
static boolean isValid(String s) {
    Deque<Character> st = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c == '(' || c == '[' || c == '{') st.push(c);
        else {
            if (st.isEmpty()) return false;
            char o = st.pop();
            if ((c == ')' && o != '(') || (c == ']' && o != '[') || (c == '}' && o != '{')) return false;
        }
    }
    return st.isEmpty();
}
```

## J3 ★★★ Find duplicate elements using Java 8 Streams (mock-e 10 marks chhilo)
```java
static Set<Integer> findDuplicates(List<Integer> list) {
    return list.stream()
        .collect(Collectors.groupingBy(x -> x, Collectors.counting()))
        .entrySet().stream()
        .filter(e -> e.getValue() > 1)
        .map(Map.Entry::getKey)
        .collect(Collectors.toSet());
}

// shorter, but stateful (use only on sequential streams)
static Set<Integer> findDuplicatesSet(List<Integer> list) {
    Set<Integer> seen = new HashSet<>();
    return list.stream().filter(x -> !seen.add(x)).collect(Collectors.toSet());
}

// remove duplicates, keep order
static List<Integer> removeDuplicates(List<Integer> list) {
    return list.stream().distinct().collect(Collectors.toList());
}
```
Explain: `groupingBy + counting` stateless, parallel-safe; O(n).

## J4 ★★★ First non-repeating character — O(n)
```java
static Character firstNonRepeating(String s) {
    Map<Character, Integer> m = charFrequency(s);          // J9
    for (Map.Entry<Character, Integer> e : m.entrySet()) if (e.getValue() == 1) return e.getKey();
    return null;
}
```
`LinkedHashMap` insertion order rakhe, tai first non-repeating paoa jay.

## J5 ★★★ Palindrome (ignore case and non-alphanumeric)
```java
static boolean isPalindrome(String s) {
    int i = 0, j = s.length() - 1;
    while (i < j) {
        while (i < j && !Character.isLetterOrDigit(s.charAt(i))) i++;
        while (i < j && !Character.isLetterOrDigit(s.charAt(j))) j--;
        if (Character.toLowerCase(s.charAt(i++)) != Character.toLowerCase(s.charAt(j--))) return false;
    }
    return true;
}
```

## J6 ★★★ Maximum subarray sum (Kadane) — O(n), O(1)
```java
static int maxSubarray(int[] a) {
    int cur = a[0], best = a[0];
    for (int i = 1; i < a.length; i++) {
        cur = Math.max(a[i], cur + a[i]);
        best = Math.max(best, cur);
    }
    return best;
}
```

## J7 ★★★ Binary search (sorted array) — O(log n)
```java
static int binarySearch(int[] a, int key) {
    int lo = 0, hi = a.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;          // overflow-safe
        if (a[mid] == key) return mid;
        if (a[mid] < key) lo = mid + 1; else hi = mid - 1;
    }
    return -1;
}
```

## J8 ★★ Merge two sorted arrays — O(n+m)
```java
static int[] mergeSorted(int[] a, int[] b) {
    int[] r = new int[a.length + b.length];
    int i = 0, j = 0, k = 0;
    while (i < a.length && j < b.length) r[k++] = a[i] <= b[j] ? a[i++] : b[j++];
    while (i < a.length) r[k++] = a[i++];
    while (j < b.length) r[k++] = b[j++];
    return r;
}
```

## J9 ★★ Character frequency
```java
static Map<Character, Integer> charFrequency(String s) {
    Map<Character, Integer> m = new LinkedHashMap<>();
    for (char c : s.toCharArray()) m.merge(c, 1, Integer::sum);
    return m;
}
```

## J10 ★★ Second largest distinct number — single pass
```java
static Integer secondLargest(int[] a) {
    Integer first = null, second = null;
    for (int x : a) {
        if (first == null || x > first) { second = first; first = x; }
        else if (x != first && (second == null || x > second)) second = x;
    }
    return second;                 // null if not present
}
```

## J11 ★★ Anagram check (lowercase a–z) — O(n)
```java
static boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) return false;
    int[] c = new int[26];
    for (int i = 0; i < a.length(); i++) { c[a.charAt(i) - 'a']++; c[b.charAt(i) - 'a']--; }
    for (int x : c) if (x != 0) return false;
    return true;
}
```

## J12 ★★ Merge overlapping intervals — O(n log n)
```java
static int[][] mergeIntervals(int[][] in) {
    if (in.length == 0) return in;
    int[][] s = in.clone();
    Arrays.sort(s, (x, y) -> Integer.compare(x[0], y[0]));
    List<int[]> out = new ArrayList<>();
    int[] cur = s[0].clone();
    for (int i = 1; i < s.length; i++) {
        if (s[i][0] <= cur[1]) cur[1] = Math.max(cur[1], s[i][1]);
        else { out.add(cur); cur = s[i].clone(); }
    }
    out.add(cur);
    return out.toArray(new int[0][]);
}
```

## J13 ★★ LRU cache — O(1) get/put
```java
static class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int cap;
    LRUCache(int cap) { super(16, 0.75f, true); this.cap = cap; }   // true = access order
    @Override protected boolean removeEldestEntry(Map.Entry<K, V> e) { return size() > cap; }
}
```
(Not thread-safe; wrap with `Collections.synchronizedMap` ba lock.) Interviewer "without LinkedHashMap" chaile: `HashMap` + doubly linked list.

## J14 ★★ Thread-safe counter
```java
static class SafeCounter {
    private final AtomicInteger c = new AtomicInteger();
    int increment() { return c.incrementAndGet(); }      // lock-free CAS
    int get() { return c.get(); }
}
```
Alternative: `synchronized int increment() { return ++count; }`. `volatile` alone `count++` atomic kore na (read-modify-write).

## J15 ★★ Streams on employees (groupingBy, sort, average)
```java
record Emp(String name, String dept, double salary) {}

static Map<String, Optional<Emp>> topPaidPerDept(List<Emp> l) {
    return l.stream().collect(Collectors.groupingBy(Emp::dept,
            Collectors.maxBy(Comparator.comparingDouble(Emp::salary))));
}
static Map<String, Double> avgSalaryPerDept(List<Emp> l) {
    return l.stream().collect(Collectors.groupingBy(Emp::dept, TreeMap::new,
            Collectors.averagingDouble(Emp::salary)));
}
static List<String> namesBySalaryDesc(List<Emp> l) {
    return l.stream()
            .sorted(Comparator.comparingDouble(Emp::salary).reversed().thenComparing(Emp::name))
            .map(Emp::name).collect(Collectors.toList());
}
```

## J16 ★ Reverse a string / words
```java
static String reverse(String s) { return new StringBuilder(s).reverse().toString(); }

static String reverseWords(String s) {
    String[] w = s.trim().split("\\s+");
    Collections.reverse(Arrays.asList(w));
    return String.join(" ", w);
}
```

## J17 ★ FizzBuzz
```java
static List<String> fizzBuzz(int n) {
    List<String> r = new ArrayList<>();
    for (int i = 1; i <= n; i++)
        r.add(i % 15 == 0 ? "FizzBuzz" : i % 3 == 0 ? "Fizz" : i % 5 == 0 ? "Buzz" : String.valueOf(i));
    return r;
}
```

## J18 ★ Missing number (array has n distinct numbers from 0..n)
```java
static int missingNumber(int[] a) {
    int sum = a.length * (a.length + 1) / 2;
    for (int x : a) sum -= x;
    return sum;
}
```

## J19 ★ Prime and Fibonacci
```java
static boolean isPrime(int n) {
    if (n < 2) return false;
    for (int i = 2; (long) i * i <= n; i++) if (n % i == 0) return false;
    return true;
}
static long fib(int n) {
    long a = 0, b = 1;
    for (int i = 0; i < n; i++) { long t = a + b; a = b; b = t; }
    return a;
}
```

## J20 ★ Thread-safe Singleton (initialization-on-demand holder)
```java
static final class Config {
    private Config() {}
    private static class Holder { static final Config INSTANCE = new Config(); }
    static Config getInstance() { return Holder.INSTANCE; }
}
```
Lazy + thread-safe (class loading guarantee), no `synchronized` cost. Alternative: single-element `enum`.

## J21 ★ "Outline a CRUD REST API" (Spring Boot) — syntax-te choto, structure-e shothik
*(Compile-verified na — Spring dependency lage; structure mukhosto koro.)*
```java
@Entity @Table(name = "employees")
public class Employee {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    @NotBlank @Size(max = 120) private String name;
    @PositiveOrZero private BigDecimal salary;
    @Version private Long version;                       // optimistic locking
    // getters/setters
}

public interface EmployeeRepository extends JpaRepository<Employee, Long> {}

@Service
public class EmployeeService {
    private final EmployeeRepository repo;
    public EmployeeService(EmployeeRepository repo) { this.repo = repo; }   // constructor injection

    @Transactional(readOnly = true)
    public Page<Employee> list(Pageable p) { return repo.findAll(p); }

    @Transactional
    public Employee create(Employee e) { return repo.save(e); }

    @Transactional(readOnly = true)
    public Employee get(Long id) {
        return repo.findById(id).orElseThrow(() -> new NotFoundException("Employee " + id));
    }
    @Transactional public void delete(Long id) { repo.delete(get(id)); }
}

@RestController @RequestMapping("/api/v1/employees")
public class EmployeeController {
    private final EmployeeService svc;
    public EmployeeController(EmployeeService svc) { this.svc = svc; }

    @GetMapping            public Page<Employee> list(Pageable p) { return svc.list(p); }          // 200
    @GetMapping("/{id}")   public Employee get(@PathVariable Long id) { return svc.get(id); }      // 200 / 404
    @PostMapping           public ResponseEntity<Employee> create(@Valid @RequestBody Employee e) {
        Employee saved = svc.create(e);
        return ResponseEntity.created(URI.create("/api/v1/employees/" + saved.getId())).body(saved); // 201
    }
    @DeleteMapping("/{id}") @ResponseStatus(HttpStatus.NO_CONTENT)
    public void delete(@PathVariable Long id) { svc.delete(id); }                                  // 204
}

@RestControllerAdvice
class GlobalExceptionHandler {
    @ExceptionHandler(NotFoundException.class)
    ResponseEntity<Map<String,String>> notFound(NotFoundException ex) {
        return ResponseEntity.status(404).body(Map.of("error", ex.getMessage()));
    }
    @ExceptionHandler(MethodArgumentNotValidException.class)
    ResponseEntity<Map<String,String>> invalid(MethodArgumentNotValidException ex) {
        return ResponseEntity.badRequest().body(Map.of("error", "Validation failed"));  // 400
    }
}
```
Marks-er jonno bolo: DTO use (entity directly expose na), pagination, `@Valid`, proper status codes, constructor injection, `@Transactional` service layer-e, auth (`@PreAuthorize`) ar tenant scope.

---

## Section C — exam-e kibhabe likhbe (checklist)
1. Problem 1 line-e restate + assumption (sorted? duplicates? null?).
2. Code/SQL — readable naming, comment kom.
3. **Complexity** (time/space) likho — marks hara joto-e na.
4. 2–3 edge case (empty, single element, duplicates, NULL, ties).
5. SQL-e: JOIN type keno, NULL/tie behaviour, index suggestion 1 line.
