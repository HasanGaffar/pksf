# Section C — SQL & Coding Practice (Solutions Included)

## SQL schema assumed
```sql
CREATE TABLE departments (
  id BIGINT PRIMARY KEY,
  name VARCHAR(120) NOT NULL
);
CREATE TABLE employees (
  id BIGINT PRIMARY KEY,
  name VARCHAR(120) NOT NULL,
  department_id BIGINT REFERENCES departments(id),
  salary NUMERIC(12,2),
  manager_id BIGINT REFERENCES employees(id),
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

## SQL problems

### 1. Employees with department names
```sql
SELECT e.id, e.name, d.name AS department
FROM employees e
LEFT JOIN departments d ON d.id = e.department_id;
```

### 2. Departments with no employees
```sql
SELECT d.*
FROM departments d
LEFT JOIN employees e ON e.department_id = d.id
WHERE e.id IS NULL;
```

### 3. Employee count per department, including empty departments
```sql
SELECT d.id, d.name, COUNT(e.id) AS total
FROM departments d
LEFT JOIN employees e ON e.department_id = d.id
GROUP BY d.id, d.name;
```

### 4. Highest salary
```sql
SELECT MAX(salary) AS highest_salary FROM employees;
```

### 5. All employees with the highest salary
```sql
SELECT * FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees);
```

### 6. Second-highest distinct salary
```sql
SELECT DISTINCT salary FROM employees
WHERE salary IS NOT NULL
ORDER BY salary DESC OFFSET 1 LIMIT 1;
```

### 7. Nth-highest distinct salary
```sql
SELECT salary FROM (
  SELECT DISTINCT salary,
         DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM employees WHERE salary IS NOT NULL
) s WHERE rnk = :n;
```

### 8. Top three salaries in each department, ties included
```sql
SELECT id, name, department_id, salary
FROM (
  SELECT e.*,
         DENSE_RANK() OVER (
           PARTITION BY department_id ORDER BY salary DESC
         ) AS rnk
  FROM employees e
) x WHERE rnk <= 3;
```

### 9. Employees earning above department average
```sql
SELECT e.*
FROM employees e
JOIN (
  SELECT department_id, AVG(salary) avg_salary
  FROM employees GROUP BY department_id
) a USING (department_id)
WHERE e.salary > a.avg_salary;
```

### 10. Duplicate employee names
```sql
SELECT name, COUNT(*) FROM employees
GROUP BY name HAVING COUNT(*) > 1;
```

### 11. Latest five employees
```sql
SELECT * FROM employees
ORDER BY created_at DESC, id DESC LIMIT 5;
```

### 12. Employees hired in the last 30 days
```sql
SELECT * FROM employees
WHERE created_at >= now() - INTERVAL '30 days';
```

### 13. Monthly employee count
```sql
SELECT date_trunc('month', created_at) AS month,
       COUNT(*) AS total
FROM employees
GROUP BY 1 ORDER BY 1;
```

### 14. Running salary total
```sql
SELECT id, name, salary,
       SUM(salary) OVER (
         ORDER BY created_at, id
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_total
FROM employees;
```

### 15. Rank employees by salary
```sql
SELECT id, name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

### 16. Employee and manager names
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON m.id = e.manager_id;
```

### 17. Recursive organization tree
```sql
WITH RECURSIVE org AS (
  SELECT id, name, manager_id, 0 depth
  FROM employees WHERE manager_id IS NULL
  UNION ALL
  SELECT e.id, e.name, e.manager_id, o.depth + 1
  FROM employees e JOIN org o ON e.manager_id = o.id
)
SELECT * FROM org ORDER BY depth, id;
```

### 18. Conditional aggregation
```sql
SELECT department_id,
       COUNT(*) AS all_staff,
       COUNT(*) FILTER (WHERE salary >= 50000) AS higher_paid
FROM employees GROUP BY department_id;
```

### 19. Upsert department
```sql
INSERT INTO departments(id, name)
VALUES (:id, :name)
ON CONFLICT (id)
DO UPDATE SET name = EXCLUDED.name;
```

### 20. Delete duplicates but keep the lowest id
```sql
WITH ranked AS (
  SELECT id, ROW_NUMBER() OVER (
    PARTITION BY name ORDER BY id
  ) rn
  FROM employees
)
DELETE FROM employees e USING ranked r
WHERE e.id = r.id AND r.rn > 1;
```
Run only after confirming the business definition of duplicate and taking a backup.

### 21. Keyset pagination
```sql
SELECT id, name, created_at
FROM employees
WHERE (created_at, id) < (:last_created_at, :last_id)
ORDER BY created_at DESC, id DESC
LIMIT 25;
```

### 22. Create a useful composite index
```sql
CREATE INDEX idx_employee_dept_created
ON employees (department_id, created_at DESC, id DESC);
```
Choose indexes from measured query patterns, not by guesswork.

### 23. Find rows with NULL salary
```sql
SELECT * FROM employees WHERE salary IS NULL;
```

### 24. Safe transfer transaction pattern
```sql
BEGIN;
UPDATE accounts
SET balance = balance - :amount
WHERE id = :from_id AND balance >= :amount;
-- Application must verify exactly one row changed.
UPDATE accounts
SET balance = balance + :amount
WHERE id = :to_id;
-- Verify destination row count, then COMMIT; otherwise ROLLBACK.
COMMIT;
```

### 25. Investigate a slow query
```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM employees
WHERE department_id = 10
ORDER BY created_at DESC LIMIT 50;
```
EXPLAIN ANALYZE executes the statement; use care with write queries.

## Java coding problems with solutions

### 26. Reverse a string
```java
String reverse(String s) {
    return new StringBuilder(s).reverse().toString();
}
```

### 27. Palindrome
```java
boolean palindrome(String s) {
    String x = s.replaceAll("[^A-Za-z0-9]", "").toLowerCase();
    return x.equals(new StringBuilder(x).reverse().toString());
}
```

### 28. Two Sum
```java
int[] twoSum(int[] a, int target) {
    Map<Integer,Integer> seen = new HashMap<>();
    for (int i = 0; i < a.length; i++) {
        int need = target - a[i];
        if (seen.containsKey(need)) return new int[]{seen.get(need), i};
        seen.put(a[i], i);
    }
    return new int[]{-1, -1};
}
```
Average O(n) time, O(n) space.

### 29. Character frequency
```java
Map<Character, Long> frequency(String s) {
    return s.chars().mapToObj(c -> (char)c)
        .collect(Collectors.groupingBy(
            Function.identity(), LinkedHashMap::new, Collectors.counting()));
}
```

### 30. First non-repeating character
```java
Optional<Character> firstUnique(String s) {
    Map<Character, Long> m = s.chars().mapToObj(c -> (char)c)
        .collect(Collectors.groupingBy(
            Function.identity(), LinkedHashMap::new, Collectors.counting()));
    return m.entrySet().stream().filter(e -> e.getValue() == 1)
        .map(Map.Entry::getKey).findFirst();
}
```

### 31. Second-largest distinct number
```java
OptionalInt secondLargest(int[] a) {
    return Arrays.stream(a).distinct().sorted()
        .skip(Math.max(0, Arrays.stream(a).distinct().count() - 2))
        .findFirst();
}
```
For a production interview, explain the one-pass O(n) alternative and empty/single-distinct-value cases.

### 32. Binary search
```java
int binarySearch(int[] a, int target) {
    int lo = 0, hi = a.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == target) return mid;
        if (a[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}
```
O(log n) time, O(1) space.

### 33. Merge sorted arrays
```java
int[] merge(int[] a, int[] b) {
    int[] out = new int[a.length + b.length];
    int i=0, j=0, k=0;
    while (i<a.length && j<b.length)
        out[k++] = a[i] <= b[j] ? a[i++] : b[j++];
    while (i<a.length) out[k++] = a[i++];
    while (j<b.length) out[k++] = b[j++];
    return out;
}
```

### 34. Valid parentheses
```java
boolean valid(String s) {
    Deque<Character> st = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c=='(' || c=='[' || c=='{') st.push(c);
        else {
            if (st.isEmpty()) return false;
            char x=st.pop();
            if ((c==')' && x!='(') || (c==']' && x!='[') ||
                (c=='}' && x!='{')) return false;
        }
    }
    return st.isEmpty();
}
```

### 35. Maximum subarray sum (Kadane)
```java
int maxSubArray(int[] a) {
    int best=a[0], current=a[0];
    for (int i=1; i<a.length; i++) {
        current=Math.max(a[i], current+a[i]);
        best=Math.max(best, current);
    }
    return best;
}
```

### 36. Remove duplicates preserving insertion order
```java
List<Integer> unique(int[] a) {
    return new ArrayList<>(new LinkedHashSet<>(
        Arrays.stream(a).boxed().collect(Collectors.toList())));
}
```

### 37. FizzBuzz
```java
for (int i=1; i<=100; i++) {
    if (i%15==0) System.out.println("FizzBuzz");
    else if (i%3==0) System.out.println("Fizz");
    else if (i%5==0) System.out.println("Buzz");
    else System.out.println(i);
}
```

### 38. Merge overlapping intervals
```java
int[][] mergeIntervals(int[][] a) {
    if (a.length == 0) return new int[0][];
    Arrays.sort(a, Comparator.comparingInt(x -> x[0]));
    List<int[]> out = new ArrayList<>();
    int[] cur = a[0].clone();
    for (int i=1; i<a.length; i++) {
        if (a[i][0] <= cur[1]) cur[1] = Math.max(cur[1], a[i][1]);
        else { out.add(cur); cur=a[i].clone(); }
    }
    out.add(cur);
    return out.toArray(new int[out.size()][]);
}
```

### 39. LRU cache
```java
class Lru<K,V> extends LinkedHashMap<K,V> {
    private final int capacity;
    Lru(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }
    protected boolean removeEldestEntry(Map.Entry<K,V> e) {
        return size() > capacity;
    }
}
```
This basic implementation is not thread-safe; synchronize or use a suitable cache library in concurrent production code.

### 40. Thread-safe counter
```java
class Counter {
    private final AtomicLong value = new AtomicLong();
    long increment() { return value.incrementAndGet(); }
    long get() { return value.get(); }
}
```

## Coding evaluation checklist
- Clarify input constraints and null/empty cases.
- State time and space complexity.
- Test duplicates, negative values, boundaries and overflow.
- Prefer readable code and meaningful names.
- For database writes, discuss transaction boundaries and concurrency.
