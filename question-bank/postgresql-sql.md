# PostgreSQL & SQL — Question Bank with Answers

## Concepts

### 1. INNER JOIN vs LEFT JOIN?
**Answer:** INNER JOIN returns only matching rows. LEFT JOIN returns every left-side row plus matching right-side values; unmatched right-side columns are NULL.

### 2. WHERE vs HAVING?
**Answer:** WHERE filters rows before grouping; HAVING filters groups after aggregation. Use WHERE for row predicates whenever possible.

### 3. DELETE vs TRUNCATE vs DROP?
**Answer:** DELETE removes selected rows and can use WHERE; TRUNCATE removes all rows efficiently and is transactional in PostgreSQL; DROP removes the database object. Consider foreign keys, triggers, identity reset and permissions.

### 4. Primary key vs unique constraint?
**Answer:** A primary key uniquely identifies each row and is NOT NULL; a table has one primary key, possibly composite. Multiple unique constraints are allowed. PostgreSQL unique constraints normally allow multiple NULLs unless NULLS NOT DISTINCT is specified.

### 5. What is normalization?
**Answer:** 1NF stores atomic values/no repeating groups; 2NF removes partial dependency on part of a composite key; 3NF removes transitive dependency of non-key attributes. Normalize to reduce anomalies; selectively denormalize for measured reporting needs.

### 6. Explain ACID.
**Answer:** Atomicity is all-or-nothing; Consistency preserves constraints; Isolation controls concurrent transaction interference; Durability preserves committed changes.

### 7. What is an index?
**Answer:** An index can reduce lookup/sort cost at the expense of storage and write overhead. Choose based on actual predicates, joins and ordering; inspect plans rather than indexing every column.

### 8. B-tree vs GIN?
**Answer:** B-tree is the general default for equality, range and ordering. GIN is useful for arrays, JSONB containment and full-text search. Choose based on operators and workload.

### 9. What is a composite index?
**Answer:** An index over multiple columns. Column order matters; a B-tree on (a,b) is most directly useful for predicates beginning with a. Design from common filters and ordering patterns.

### 10. What does EXPLAIN ANALYZE do?
**Answer:** EXPLAIN shows planned operations; EXPLAIN ANALYZE executes the query and reports actual timings/rows. BUFFERS shows buffer activity. Remember ANALYZE executes the statement; use care with writes.

### 11. What is a deadlock?
**Answer:** Two or more transactions wait on locks held by each other. PostgreSQL detects a cycle and aborts one transaction. Reduce risk by acquiring locks in consistent order, keeping transactions short and retrying safe transactions.

### 12. Isolation levels?
**Answer:** PostgreSQL supports Read Committed (default), Repeatable Read and Serializable; Read Uncommitted behaves as Read Committed. Higher isolation may cause serialization failures; retry appropriate transactions.

### 13. What is a CTE?
**Answer:** WITH defines a named subquery. WITH RECURSIVE supports recursive traversal. CTEs improve readability; planner behaviour/materialization depends on PostgreSQL version and query form.

### 14. OFFSET vs keyset pagination?
**Answer:** OFFSET is simple but large offsets can be expensive and results can shift under concurrent writes. Keyset pagination uses stable ordered keys and a cursor predicate, usually scaling better for deep pages.

## SQL exercises and solutions

Assume employees(id, name, department_id, salary, manager_id, created_at) and departments(id, department_name).

### 15. Department employee count and average salary
~~~sql
SELECT d.id, d.department_name,
       COUNT(e.id) AS employee_count,
       COALESCE(AVG(e.salary), 0) AS avg_salary
FROM departments d
LEFT JOIN employees e ON e.department_id = d.id
GROUP BY d.id, d.department_name
ORDER BY d.id;
~~~

### 16. Second-highest distinct salary
~~~sql
SELECT salary
FROM (SELECT DISTINCT salary FROM employees) s
ORDER BY salary DESC
OFFSET 1 LIMIT 1;
~~~

### 17. Top 3 salaries per department, including ties
~~~sql
SELECT id, name, department_id, salary
FROM (
  SELECT e.*,
         DENSE_RANK() OVER (
           PARTITION BY department_id ORDER BY salary DESC
         ) AS salary_rank
  FROM employees e
) ranked
WHERE salary_rank <= 3
ORDER BY department_id, salary DESC;
~~~

### 18. Find duplicate names
~~~sql
SELECT name, COUNT(*) AS occurrences
FROM employees
GROUP BY name
HAVING COUNT(*) > 1;
~~~

### 19. Employees earning above department average
~~~sql
SELECT e.*
FROM employees e
JOIN (
  SELECT department_id, AVG(salary) AS avg_salary
  FROM employees
  GROUP BY department_id
) a USING (department_id)
WHERE e.salary > a.avg_salary;
~~~

### 20. Latest five employees
~~~sql
SELECT id, name, created_at
FROM employees
ORDER BY created_at DESC, id DESC
LIMIT 5;
~~~

### 21. Recursive employee reporting tree
~~~sql
WITH RECURSIVE org_tree AS (
  SELECT id, name, manager_id, 0 AS depth
  FROM employees
  WHERE manager_id IS NULL
  UNION ALL
  SELECT e.id, e.name, e.manager_id, t.depth + 1
  FROM employees e
  JOIN org_tree t ON e.manager_id = t.id
)
SELECT * FROM org_tree ORDER BY depth, id;
~~~

### 22. Upsert
~~~sql
INSERT INTO departments (id, department_name)
VALUES (10, 'Technology')
ON CONFLICT (id)
DO UPDATE SET department_name = EXCLUDED.department_name;
~~~

### 23. Employees with no matching department
~~~sql
SELECT e.*
FROM employees e
LEFT JOIN departments d ON d.id = e.department_id
WHERE d.id IS NULL;
~~~

### 24. Keyset pagination
~~~sql
SELECT id, name, created_at
FROM employees
WHERE (created_at, id) < (:last_created_at, :last_id)
ORDER BY created_at DESC, id DESC
LIMIT 20;
~~~
Use a matching composite index and cursor values from the last row of the previous page.

### 25. Transaction example
~~~sql
BEGIN;
UPDATE accounts
SET balance = balance - 1000
WHERE id = 1 AND balance >= 1000;
-- Application must verify exactly one row was updated.
UPDATE accounts
SET balance = balance + 1000
WHERE id = 2;
COMMIT;
~~~
In production, check affected row counts and handle errors/rollback in application code; protect concurrent balance changes with constraints and suitable locking/isolation.

## Query tuning checklist
1. Capture the slow query and representative parameters.
2. Run EXPLAIN (ANALYZE, BUFFERS) in a safe environment.
3. Compare estimated vs actual rows; inspect scans, joins, sorts and spills.
4. Check statistics, table size, bloat and index selectivity.
5. Rewrite predicates to be index-friendly where appropriate.
6. Avoid unnecessary columns, rows and repeated round trips.
7. Measure after each change; monitor write overhead.
