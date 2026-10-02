# Database Interview Preparation Guide (End to End)
### DBMS Fundamentals → SQL → Advanced SQL → PostgreSQL → MongoDB → Coding Queries → Node.js Integration | Questions + Simple Answers + Examples

> **How to use this document**
> - **Part 1–3:** DB fundamentals + SQL basics + joins/subqueries/window functions (asked in **every** interview)
> - **Part 4–5:** Advanced SQL (indexes, transactions, normalization, optimization)
> - **Part 6:** PostgreSQL specific
> - **Part 7–8:** MongoDB (CRUD, aggregation, indexing, schema design, replication, sharding)
> - **Part 9:** SQL query practice (the most common live-coding questions)
> - **Part 10–12:** Node.js integration, scenarios/HR, cheat sheet
> - Answer format that impresses: **Definition → Why/When we use it → Small example → "In my project I used..."**
> - Where you haven't used something, say: *"I haven't used it in depth, but my understanding is..."*

---

## Table of Contents
1. [Database Fundamentals](#part-1--database-fundamentals)
2. [SQL Basics](#part-2--sql-basics)
3. [Joins, Subqueries, CTEs and Window Functions](#part-3--joins-subqueries-ctes-and-window-functions)
4. [Indexes, Views, Procedures, Triggers](#part-4--indexes-views-procedures-and-triggers)
5. [Transactions, Normalization and Optimization](#part-5--transactions-normalization-and-optimization)
6. [PostgreSQL](#part-6--postgresql)
7. [MongoDB Basics and CRUD](#part-7--mongodb-basics-and-crud)
8. [MongoDB Advanced (Aggregation, Indexing, Schema Design, Replication, Sharding)](#part-8--mongodb-advanced)
9. [SQL Query Practice](#part-9--sql-query-practice)
10. [Using Databases with Node.js](#part-10--using-databases-with-nodejs)
11. [Scenario-Based and HR Questions](#part-11--scenario-based-and-hr-questions)
12. [Last-Minute Cheat Sheet](#part-12--last-minute-cheat-sheet)

---

# Part 1 — Database Fundamentals

### Q1. What is a database? What is a DBMS?
A **database** is an organized collection of data. A **DBMS (Database Management System)** is software to store, retrieve, update and manage that data (MySQL, PostgreSQL, MongoDB, Oracle).

### Q2. What is an RDBMS?
**Relational DBMS** stores data in **tables (rows and columns)** and connects tables using **relationships (keys).** It uses SQL. Examples: PostgreSQL, MySQL, Oracle, SQL Server.

### Q3. SQL vs NoSQL?
| SQL (Relational) | NoSQL |
|---|---|
| Tables with fixed schema | Flexible schema (documents, key-value, graph, column) |
| Strong relations and joins | Relations are weak/denormalized |
| ACID transactions (strong) | Usually BASE/eventual consistency (varies) |
| Scales mostly vertically (also read replicas, sharding with effort) | Designed to scale horizontally |
| PostgreSQL, MySQL | MongoDB, Redis, Cassandra, Neo4j |

**Choose SQL** for structured data, complex queries/joins, transactions (banking, orders). **Choose NoSQL** for flexible/changing data, huge scale, fast reads/writes (catalogs, logs, real-time).

### Q4. Types of NoSQL databases?
- **Document:** MongoDB, CouchDB
- **Key-Value:** Redis, DynamoDB
- **Column-family:** Cassandra, HBase
- **Graph:** Neo4j

### Q5. What is ACID?
Properties that guarantee reliable transactions:
- **Atomicity:** all steps happen or none (all-or-nothing)
- **Consistency:** data moves from one valid state to another (rules/constraints kept)
- **Isolation:** concurrent transactions don't interfere with each other
- **Durability:** once committed, data is saved even after a crash

**Example:** Money transfer: debit A and credit B must both succeed or both fail.

### Q6. What is the CAP theorem?
In a distributed system you can fully guarantee only **two of three:** **Consistency, Availability, Partition tolerance.** Since network partitions happen, you choose between **CP** (consistent, may reject requests) or **AP** (available, may return stale data).

### Q7. What is BASE?
Used by many NoSQL systems: **Basically Available, Soft state, Eventually consistent.** (An alternative to ACID favoring availability.)

### Q8. What are the keys in a database?
- **Primary Key:** uniquely identifies each row; cannot be NULL; only one per table.
- **Foreign Key:** a column that references the primary key of another table (creates a relationship).
- **Unique Key:** values must be unique; (allows NULLs, behavior varies by DB).
- **Candidate Key:** any column(s) that could be a primary key.
- **Composite Key:** primary key made from two or more columns.
- **Surrogate Key:** artificial key (auto-increment id, UUID). **Natural Key:** real-world value (email, passport no).

### Q9. Primary key vs Unique key?
| Primary Key | Unique Key |
|---|---|
| One per table | Many per table |
| No NULL | NULL allowed |
| Identifies the row | Ensures uniqueness of a column |

### Q10. What are constraints?
Rules on data: `NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `FOREIGN KEY`, `CHECK`, `DEFAULT`.

### Q11. What are relationships in a database?
**One-to-One** (user–profile), **One-to-Many** (customer–orders), **Many-to-Many** (students–courses; needs a **junction/bridge table**).

### Q12. What is an ER diagram?
**Entity–Relationship diagram:** a visual design of entities (tables), attributes (columns) and relationships before building the database.

### Q13. What is OLTP vs OLAP?
**OLTP:** many small, fast transactions (orders, payments), normalized DB. **OLAP:** complex analysis/reporting on large historical data, data warehouse.

### Q14. What is a schema?
The **structure/blueprint** of the database (tables, columns, types, relations). In PostgreSQL, a *schema* is also a **namespace** inside a database (default: `public`).

### Q15. What is the difference between a database and a table?
A database contains many tables (and views, indexes, functions). A table stores rows of data of one entity.

### Q16. What is a foreign key action (ON DELETE)?
Defines what happens to child rows when the parent row is deleted/updated: `CASCADE` (delete children too), `SET NULL`, `RESTRICT`/`NO ACTION` (block), `SET DEFAULT`.

---

# Part 2 — SQL Basics

### Q17. What is SQL? Its sub-languages?
**Structured Query Language** for working with relational databases.
- **DDL** (Data Definition): `CREATE, ALTER, DROP, TRUNCATE`
- **DML** (Data Manipulation): `INSERT, UPDATE, DELETE` (and `SELECT` is often called DQL)
- **DCL** (Data Control): `GRANT, REVOKE`
- **TCL** (Transaction Control): `COMMIT, ROLLBACK, SAVEPOINT`

### Q18. DELETE vs TRUNCATE vs DROP?
| | DELETE | TRUNCATE | DROP |
|---|---|---|---|
| Removes | Selected rows (WHERE) or all | **All rows** | **Entire table** (structure too) |
| Type | DML | DDL | DDL |
| WHERE clause | Yes | No | No |
| Speed | Slower (row by row, logged) | Faster | Fast |
| Rollback | Yes | Yes in PostgreSQL (transactional); not in MySQL/Oracle | Depends (PG: yes in transaction) |
| Resets identity | No | Yes (`RESTART IDENTITY` in PG) | N/A |

### Q19. Create a table with common constraints (PostgreSQL syntax)?
```sql
CREATE TABLE employees (
  id          INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name        VARCHAR(100) NOT NULL,
  email       VARCHAR(150) UNIQUE NOT NULL,
  salary      NUMERIC(10,2) CHECK (salary > 0),
  dept_id     INT REFERENCES departments(id) ON DELETE SET NULL,
  hired_on    DATE DEFAULT CURRENT_DATE,
  is_active   BOOLEAN DEFAULT TRUE
);
```

### Q20. Basic CRUD in SQL?
```sql
INSERT INTO employees (name, email, salary) VALUES ('Ravi', 'ravi@x.com', 50000);
SELECT * FROM employees WHERE salary > 40000;
UPDATE employees SET salary = salary + 5000 WHERE id = 1;
DELETE FROM employees WHERE id = 1;
```
**Always use WHERE** with UPDATE/DELETE; otherwise all rows change.

### Q21. ALTER TABLE examples?
```sql
ALTER TABLE employees ADD COLUMN phone VARCHAR(15);
ALTER TABLE employees DROP COLUMN phone;
ALTER TABLE employees RENAME COLUMN name TO full_name;
ALTER TABLE employees ALTER COLUMN salary SET NOT NULL;
ALTER TABLE employees ADD CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(id);
```

### Q22. Order of SQL clauses — written vs executed?
**Written:** `SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT`
**Executed (logical order):** `FROM/JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT`
That's why you **can't use a SELECT alias in WHERE** (but can in ORDER BY).

### Q23. WHERE vs HAVING?
`WHERE` filters **rows before grouping** (can't use aggregates). `HAVING` filters **groups after GROUP BY** (can use aggregates).
```sql
SELECT dept_id, COUNT(*) FROM employees
WHERE is_active = TRUE
GROUP BY dept_id
HAVING COUNT(*) > 5;
```

### Q24. Common operators and clauses?
```sql
WHERE salary BETWEEN 30000 AND 60000
WHERE dept_id IN (1, 2, 3)
WHERE name LIKE 'A%'        -- starts with A; '%a' ends with a; '%an%' contains; '_' = one char
WHERE name ILIKE 'a%'       -- case-insensitive (PostgreSQL)
WHERE email IS NULL         -- NOT: = NULL
WHERE salary > 40000 AND dept_id = 2 OR is_active
ORDER BY salary DESC, name ASC
LIMIT 10 OFFSET 20          -- pagination (PG/MySQL)
SELECT DISTINCT dept_id FROM employees;
```

### Q25. How does NULL behave in SQL?
NULL means **unknown/missing**, not zero or empty. `NULL = NULL` is **not true** (it's unknown). Use `IS NULL` / `IS NOT NULL`. Any arithmetic with NULL gives NULL. Use `COALESCE(col, 0)` to give a default.
```sql
SELECT COALESCE(phone, 'N/A') FROM employees;
SELECT NULLIF(a, 0);   -- returns NULL if a = 0 (avoid divide by zero)
```

### Q26. What are aggregate functions?
`COUNT(), SUM(), AVG(), MIN(), MAX()`. They return **one value from many rows.** `COUNT(*)` counts all rows; `COUNT(col)` ignores NULLs; `COUNT(DISTINCT col)` counts unique values.

### Q27. What is GROUP BY?
Groups rows with the same values so aggregates work per group.
```sql
SELECT dept_id, AVG(salary) AS avg_salary, COUNT(*) AS total
FROM employees GROUP BY dept_id ORDER BY avg_salary DESC;
```
Every non-aggregated column in SELECT must be in GROUP BY.

### Q28. What is the CASE expression?
SQL's if-else.
```sql
SELECT name,
  CASE WHEN salary >= 80000 THEN 'High'
       WHEN salary >= 40000 THEN 'Medium'
       ELSE 'Low' END AS level
FROM employees;
```

### Q29. Common string, number and date functions?
```sql
UPPER(name), LOWER(name), LENGTH(name), TRIM(name), CONCAT(a,' ',b), a || b   -- || in PG
SUBSTRING(name FROM 1 FOR 3), REPLACE(name,'a','b')
ROUND(12.567, 2), CEIL(2.1), FLOOR(2.9), ABS(-5), MOD(10,3)
NOW(), CURRENT_DATE, EXTRACT(YEAR FROM hired_on), DATE_TRUNC('month', hired_on)
AGE(NOW(), hired_on), hired_on + INTERVAL '7 days'
```

### Q30. DISTINCT vs GROUP BY?
Both can remove duplicates. `DISTINCT` just removes duplicate rows; `GROUP BY` is used when you also need **aggregates.**

### Q31. UNION vs UNION ALL? Other set operations?
- `UNION` – combines results, **removes duplicates** (slower)
- `UNION ALL` – combines, **keeps duplicates** (faster)
- `INTERSECT` – rows common to both; `EXCEPT` (`MINUS` in Oracle) – rows in first but not in second
Both queries need the **same number of columns and compatible types.**

### Q32. CHAR vs VARCHAR vs TEXT?
`CHAR(n)` fixed length (padded). `VARCHAR(n)` variable length up to n. `TEXT` unlimited length. In PostgreSQL, `VARCHAR` and `TEXT` perform the same.

### Q33. What is the difference between `COUNT(*)`, `COUNT(1)` and `COUNT(column)`?
`COUNT(*)` and `COUNT(1)` count all rows (same result). `COUNT(column)` counts only non-NULL values.

### Q34. What are aliases?
Temporary names: `SELECT name AS employee_name FROM employees e;` Makes output and joins readable.

### Q35. What is SQL injection? How to prevent it?
Attacker puts SQL code in input (`' OR '1'='1`) to manipulate your query. **Prevent with parameterized queries/prepared statements**, ORMs, input validation, least-privilege DB user. **Never build SQL by string concatenation.**

---

# Part 3 — Joins, Subqueries, CTEs and Window Functions

### Q36. What are JOINs? Types?
JOINs combine rows from two or more tables using a related column.
- **INNER JOIN:** only matching rows from both tables
- **LEFT (OUTER) JOIN:** all rows from the left + matching from right (NULL if no match)
- **RIGHT (OUTER) JOIN:** all from the right + matching from left
- **FULL (OUTER) JOIN:** all rows from both (NULL where no match)
- **CROSS JOIN:** every row with every row (Cartesian product)
- **SELF JOIN:** a table joined with itself

```sql
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;

-- Employees with NO department
SELECT e.name FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id
WHERE d.id IS NULL;
```

### Q37. Explain joins with a simple example.
Tables: **A = {1,2,3}**, **B = {2,3,4}**
- INNER → {2,3}
- LEFT → {1,2,3} (1 has no match)
- RIGHT → {2,3,4}
- FULL → {1,2,3,4}

### Q38. What is a self join? Example?
Find each employee's manager (same table):
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

### Q39. INNER JOIN vs LEFT JOIN — which is faster / when to use?
INNER returns only matches. LEFT keeps unmatched left rows. Use LEFT when you need **all** records from the main table even without related data. Performance depends on indexes and data, not the keyword alone.

### Q40. `ON` vs `WHERE` in a LEFT JOIN?
Conditions on the **right table** inside `WHERE` can turn a LEFT JOIN into an INNER JOIN (rows with NULL get removed). Put right-table filters in `ON` if you want to keep unmatched left rows.

### Q41. What is a subquery? Types?
A query inside another query.
- **Scalar** (returns one value), **Row**, **Table** (in FROM), **Correlated** (refers to the outer query; runs per row)
```sql
SELECT name FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Correlated: employees earning more than their dept average
SELECT name FROM employees e
WHERE salary > (SELECT AVG(salary) FROM employees WHERE dept_id = e.dept_id);
```

### Q42. `IN` vs `EXISTS`?
`IN` compares to a list/result set. `EXISTS` checks if a subquery returns **any row** (stops at first match, often faster for large subqueries). `NOT IN` has a trap: if the subquery returns **NULL**, it returns no rows. Prefer **`NOT EXISTS`.**
```sql
SELECT c.name FROM customers c
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
```

### Q43. What is a CTE (WITH clause)? Recursive CTE?
A **Common Table Expression** is a named temporary result that makes complex queries readable.
```sql
WITH dept_avg AS (
  SELECT dept_id, AVG(salary) AS avg_sal FROM employees GROUP BY dept_id
)
SELECT e.name, e.salary, d.avg_sal
FROM employees e JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```
**Recursive CTE** is used for hierarchies (org chart, categories):
```sql
WITH RECURSIVE org AS (
  SELECT id, name, manager_id, 1 AS level FROM employees WHERE manager_id IS NULL
  UNION ALL
  SELECT e.id, e.name, e.manager_id, o.level + 1
  FROM employees e JOIN org o ON e.manager_id = o.id
)
SELECT * FROM org;
```

### Q44. What are window functions? (VERY IMPORTANT)
Functions that calculate over a **set of related rows (the "window") without collapsing rows** (unlike GROUP BY). Syntax: `function() OVER (PARTITION BY ... ORDER BY ...)`.
Common ones:
- **Ranking:** `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE(n)`
- **Value:** `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()`
- **Aggregate as window:** `SUM() OVER`, `AVG() OVER`, `COUNT() OVER`

### Q45. ROW_NUMBER vs RANK vs DENSE_RANK?
For salaries 100, 100, 90:
| | ROW_NUMBER | RANK | DENSE_RANK |
|---|---|---|---|
| 100 | 1 | 1 | 1 |
| 100 | 2 | 1 | 1 |
| 90 | 3 | **3** (gap) | **2** (no gap) |

### Q46. Top-N per group (e.g., top 3 salaries in each department)?
```sql
SELECT * FROM (
  SELECT name, dept_id, salary,
         DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
  FROM employees
) t
WHERE rnk <= 3;
```

### Q47. Running total and previous/next row?
```sql
SELECT order_date, amount,
  SUM(amount) OVER (ORDER BY order_date) AS running_total,
  LAG(amount)  OVER (ORDER BY order_date) AS prev_amount,
  LEAD(amount) OVER (ORDER BY order_date) AS next_amount
FROM orders;
```

### Q48. GROUP BY vs window function?
GROUP BY **reduces rows** (one row per group). A window function **keeps all rows** and adds a calculated column.

### Q49. What is a view? Materialized view?
- **View:** a **saved SELECT query** that acts like a virtual table (no data stored). Used for simplification and security.
- **Materialized view:** **stores the result physically** (fast reads); must be **refreshed** (`REFRESH MATERIALIZED VIEW`). Supported in PostgreSQL.
```sql
CREATE VIEW active_employees AS SELECT id, name FROM employees WHERE is_active;
```

---

# Part 4 — Indexes, Views, Procedures and Triggers

### Q50. What is an index? Why use it?
A data structure (usually a **B-tree**) that lets the database **find rows faster without scanning the whole table**, like a book index.
```sql
CREATE INDEX idx_emp_email ON employees(email);
CREATE UNIQUE INDEX idx_emp_email_u ON employees(email);
CREATE INDEX idx_emp_dept_sal ON employees(dept_id, salary);   -- composite
```
**Cost:** uses extra storage and **slows INSERT/UPDATE/DELETE** a bit (index must be updated).

### Q51. When should you create an index? When not?
**Create on:** columns used in `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY`, and foreign keys; columns with high selectivity (many unique values).
**Avoid on:** very small tables, columns with few distinct values (like boolean), tables with heavy writes, columns rarely queried.

### Q52. Clustered vs non-clustered index?
- **Clustered:** data rows are **physically stored in index order** (one per table; e.g., InnoDB primary key, SQL Server).
- **Non-clustered:** separate structure that **points to** the data rows (many per table).
*PostgreSQL stores table data in a heap; all indexes are separate (there is a `CLUSTER` command that reorders once, but doesn't maintain order).*

### Q53. What is a composite index and the left-most prefix rule?
An index on multiple columns `(a, b, c)` helps queries filtering on `a`, or `a,b`, or `a,b,c` — **not** on `b` or `c` alone. Order of columns matters.

### Q54. What is a covering index?
An index that contains **all columns the query needs**, so the DB reads only the index (**index-only scan**), never the table.

### Q55. Why might an index not be used?
Functions on the column (`WHERE LOWER(email) = ...` without a function index), leading wildcard `LIKE '%abc'`, type mismatch, tiny table (seq scan cheaper), low selectivity, outdated statistics (`ANALYZE`), or wrong column order in a composite index.

### Q56. What is a stored procedure vs a function?
| Stored Procedure | Function |
|---|---|
| Performs actions; may or may not return a value | Must return a value |
| Can manage transactions (COMMIT) | Generally can't (in PG, procedures can; functions can't) |
| Called with `CALL` | Used inside SQL (`SELECT fn()`) |

```sql
-- PostgreSQL function
CREATE FUNCTION get_bonus(sal NUMERIC) RETURNS NUMERIC AS $$
  SELECT sal * 0.10;
$$ LANGUAGE SQL;
SELECT name, get_bonus(salary) FROM employees;
```

### Q57. What is a trigger?
Code that runs **automatically** on INSERT/UPDATE/DELETE (BEFORE/AFTER). Used for auditing, auto-timestamps, validation. Use sparingly (hidden logic, hard to debug).

### Q58. What is a cursor?
A pointer to process query results **row by row** (used in procedural code). Avoid when set-based SQL can do the job (cursors are slow).

### Q59. What are temporary tables?
Tables that exist only for a session/transaction (`CREATE TEMP TABLE`), useful for intermediate results.

### Q60. What is the difference between a view and a table? Can we update a view?
A table stores data; a view stores a query. Simple views (single table, no aggregates) can be updatable; complex ones usually not.

---

# Part 5 — Transactions, Normalization and Optimization

### Q61. What is a transaction?
A group of SQL statements executed as **one unit** (all or nothing).
```sql
BEGIN;
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;      -- or ROLLBACK; if something fails
```
`SAVEPOINT` lets you roll back part of a transaction.

### Q62. What are transaction isolation levels?
Control how much one transaction can see another's uncommitted/in-progress changes.
| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed | No | Possible | Possible |
| Repeatable Read | No | No | Possible (not in PostgreSQL) |
| Serializable | No | No | No |

**Defaults:** PostgreSQL = **Read Committed**; MySQL (InnoDB) = **Repeatable Read.** (PostgreSQL treats Read Uncommitted same as Read Committed; its Repeatable Read also prevents phantoms.)

### Q63. Explain dirty read, non-repeatable read, phantom read.
- **Dirty read:** reading data another transaction hasn't committed yet.
- **Non-repeatable read:** same row read twice in a transaction gives **different values** (someone updated it in between).
- **Phantom read:** same query run twice returns **different set of rows** (someone inserted/deleted rows).

### Q64. What is a deadlock? How to avoid?
Two transactions each wait for a lock held by the other, so neither can proceed. The DB detects it and **aborts one.** Avoid by: accessing tables/rows in the **same order**, keeping transactions **short**, using proper indexes, and retrying on deadlock errors.

### Q65. What are locks? Types?
Mechanisms to control concurrent access: **shared (read) locks**, **exclusive (write) locks**, row-level vs table-level. `SELECT ... FOR UPDATE` locks selected rows until the transaction ends.

### Q66. Optimistic vs pessimistic locking?
- **Pessimistic:** lock the data first (`FOR UPDATE`), assume conflicts will happen.
- **Optimistic:** don't lock; use a **version/timestamp column** and check it when updating; retry if changed.

### Q67. What is normalization? Why do we do it?
Organizing tables to **reduce redundancy** and avoid update/insert/delete anomalies.

### Q68. Explain 1NF, 2NF, 3NF, BCNF simply.
- **1NF:** each cell holds a **single (atomic) value**; no repeating groups/lists.
- **2NF:** 1NF + **no partial dependency** (every non-key column depends on the **whole** composite key).
- **3NF:** 2NF + **no transitive dependency** (non-key columns depend **only on the key**, not on other non-key columns).
- **BCNF:** stricter 3NF; every determinant must be a candidate key.

**Quick memory line:** *"Every non-key attribute must depend on the key (1NF), the whole key (2NF), and nothing but the key (3NF)."*

**Example (3NF fix):** `orders(order_id, customer_id, customer_city)` → `customer_city` depends on `customer_id`, not on `order_id`. Move it to a `customers` table.

### Q69. What is denormalization? When to use?
Adding **controlled redundancy** (duplicate columns/precomputed values) to **speed up reads** and avoid many joins. Used in reporting/read-heavy systems and NoSQL-style designs. Trade-off: harder updates, risk of inconsistency.

### Q70. How do you optimize a slow SQL query? (VERY COMMON)
1. Run **`EXPLAIN ANALYZE`** to see the plan.
2. **Add proper indexes** (WHERE, JOIN, ORDER BY columns).
3. **Select only needed columns** (avoid `SELECT *`).
4. **Filter early** and avoid functions on indexed columns.
5. Avoid **N+1 queries** (use JOIN/batch).
6. Use **pagination** (`LIMIT`); prefer **keyset pagination** for big offsets.
7. Replace `IN` subqueries with `EXISTS`/JOIN where useful.
8. Keep statistics fresh (`ANALYZE`); avoid long transactions.
9. Cache heavy results (Redis/materialized views).
10. For huge tables: **partitioning**, read replicas, archiving old data.

### Q71. What is EXPLAIN / EXPLAIN ANALYZE?
`EXPLAIN` shows the **planned** execution; `EXPLAIN ANALYZE` **actually runs** the query and shows real time and rows. Look for **Seq Scan** on big tables (needs index?), high cost, big differences between estimated and actual rows.

### Q72. What is the N+1 query problem?
Running **1 query** for the list, then **N more queries** (one per row) for related data. Fix with a **JOIN**, `IN (...)` batch query, or ORM eager loading (`include`, `populate`).

### Q73. LIMIT/OFFSET vs keyset pagination?
`OFFSET 100000` still scans and discards 100000 rows (slow). **Keyset (cursor) pagination** uses the last seen value: `WHERE id > :last_id ORDER BY id LIMIT 20` (fast, uses index).

### Q74. What is partitioning?
Splitting a **big table into smaller physical parts** (by range, list or hash) while still querying it as one table. Improves performance and maintenance (e.g., partition orders by month).

### Q75. What is sharding? Replication?
- **Sharding:** splitting data **across multiple servers** (horizontal scaling), each holds part of the data.
- **Replication:** **copying** data to other servers (primary → replicas) for **high availability and read scaling.**

### Q76. What is database connection pooling?
Keeping a pool of open connections that requests reuse, because opening a connection is expensive. Tools: `pg.Pool` in Node, **PgBouncer** for PostgreSQL.

### Q77. What is a database migration?
Versioned scripts that **change the DB schema** safely over time (Knex, Prisma Migrate, Sequelize, Flyway, Liquibase). Keeps all environments in sync.

### Q78. Backup and restore basics?
**Logical:** `pg_dump` / `mysqldump` / `mongodump` (SQL/BSON export). **Physical:** file-level/snapshot backups, plus **WAL archiving / point-in-time recovery.** Test restores regularly.

### Q79. What are ORMs? Pros and cons?
ORM maps tables to objects (Sequelize, Prisma, TypeORM, Mongoose for MongoDB). **Pros:** faster development, safer queries, migrations. **Cons:** hidden inefficient queries (N+1), less control for complex SQL. Use raw SQL when needed.

---

# Part 6 — PostgreSQL

### Q80. What is PostgreSQL? Why is it popular?
An **open-source, advanced object-relational database** known for reliability, standards compliance, and rich features. Highlights: **ACID, MVCC, JSONB, arrays, full-text search, extensions (PostGIS), custom types, window functions, CTEs, partitioning, replication,** and strong concurrency.

### Q81. PostgreSQL vs MySQL?
| PostgreSQL | MySQL |
|---|---|
| Very feature-rich, strict standards | Simpler, very popular for web apps |
| Better for complex queries, analytics, JSONB, extensions | Fast for simple read-heavy workloads |
| MVCC with heap tables | InnoDB clustered primary key |
| Default isolation: Read Committed | Default: Repeatable Read |
| Full `FULL OUTER JOIN`, `INTERSECT/EXCEPT`, `RETURNING` | (Some missing/limited historically) |

### Q82. What is MVCC? (VERY IMPORTANT for PostgreSQL)
**Multi-Version Concurrency Control:** instead of locking rows for reads, PostgreSQL keeps **multiple versions of a row.** Each transaction sees a **snapshot** of the data. **Readers don't block writers, and writers don't block readers.** An UPDATE creates a **new row version**; the old version becomes a "dead tuple."

### Q83. What is VACUUM? Why is it needed?
Because of MVCC, old row versions (dead tuples) pile up (**bloat**). **VACUUM** removes dead tuples and marks space reusable. **Autovacuum** runs automatically. `VACUUM FULL` rewrites the table and **locks it** (use rarely). `ANALYZE` updates statistics for the query planner (`VACUUM ANALYZE` does both).

### Q84. What is WAL (Write-Ahead Log)?
Changes are **written to a log first** and then to data files. It guarantees **durability** (crash recovery), and powers **replication and point-in-time recovery.**

### Q85. Important PostgreSQL data types?
`INTEGER, BIGINT, SMALLINT, NUMERIC(p,s), REAL/DOUBLE PRECISION, VARCHAR, TEXT, BOOLEAN, DATE, TIME, TIMESTAMP, TIMESTAMPTZ, UUID, JSON, JSONB, ARRAY (e.g. INT[]), ENUM, SERIAL/BIGSERIAL, BYTEA, INET`.
Use **`TIMESTAMPTZ`** (timestamp with time zone) for most date-times and **`NUMERIC`** for money (not float).

### Q86. SERIAL vs IDENTITY vs UUID?
- `SERIAL`: older shortcut for auto-increment (creates a sequence).
- `GENERATED ALWAYS AS IDENTITY`: modern, standard way (recommended).
- `UUID`: globally unique ids (`gen_random_uuid()`); good for distributed systems, but bigger and less index-friendly than integers.

### Q87. JSON vs JSONB in PostgreSQL?
| JSON | JSONB |
|---|---|
| Stored as text (exact copy) | Stored in **binary** form |
| Slower to query | **Faster**, supports **indexing (GIN)** |
| Keeps whitespace/order/duplicate keys | Doesn't keep them |
**Use JSONB** almost always.
```sql
CREATE TABLE products (id SERIAL PRIMARY KEY, data JSONB);
INSERT INTO products (data) VALUES ('{"name":"Phone","price":500,"tags":["mobile","5g"]}');

SELECT data->>'name' FROM products;                -- text value
SELECT data->'tags' FROM products;                 -- JSON value
SELECT * FROM products WHERE data @> '{"price":500}';  -- contains
CREATE INDEX idx_products_data ON products USING GIN (data);
```

### Q88. Arrays in PostgreSQL?
```sql
CREATE TABLE posts (id SERIAL PRIMARY KEY, tags TEXT[]);
INSERT INTO posts (tags) VALUES (ARRAY['sql','pg']);
SELECT * FROM posts WHERE 'sql' = ANY(tags);
SELECT * FROM posts WHERE tags @> ARRAY['sql'];
```

### Q89. Index types in PostgreSQL?
- **B-tree** (default): equality and range (`=, <, >, BETWEEN, ORDER BY`)
- **Hash:** equality only
- **GIN:** for **JSONB, arrays, full-text search** (many values per row)
- **GiST:** geometric/range data, full-text, PostGIS
- **BRIN:** very large tables with naturally ordered data (timestamps), tiny size
- **SP-GiST:** non-balanced structures
Also: **partial index** (`WHERE is_active`), **expression index** (`LOWER(email)`), **covering index** (`INCLUDE`), **unique index**, composite index.
```sql
CREATE INDEX idx_active_users ON users(email) WHERE is_active = TRUE;   -- partial
CREATE INDEX idx_lower_email ON users(LOWER(email));                    -- expression
CREATE INDEX CONCURRENTLY idx_x ON big_table(col);                      -- no write lock
```

### Q90. Scan types in EXPLAIN?
- **Seq Scan:** reads the whole table
- **Index Scan:** uses index, then fetches rows from table
- **Index Only Scan:** all data from index (needs covering index + visibility map from vacuum)
- **Bitmap Index/Heap Scan:** combines many index matches efficiently
Join methods: **Nested Loop** (small data), **Hash Join** (large, equality), **Merge Join** (sorted inputs).

### Q91. What is `RETURNING`?
Returns the affected rows from INSERT/UPDATE/DELETE in one step (very useful in APIs).
```sql
INSERT INTO users (name, email) VALUES ('Ravi','r@x.com') RETURNING id, name;
UPDATE users SET name='Ram' WHERE id=1 RETURNING *;
```

### Q92. What is UPSERT (`ON CONFLICT`)?
Insert, or update if it already exists:
```sql
INSERT INTO users (email, name) VALUES ('r@x.com', 'Ravi')
ON CONFLICT (email) DO UPDATE SET name = EXCLUDED.name;
-- or: ON CONFLICT (email) DO NOTHING;
```

### Q93. What are common `psql` commands?
```
\l            list databases          \c dbname      connect to a database
\dt           list tables             \d table       describe a table
\dn           list schemas            \di            list indexes
\du           list roles/users        \df            list functions
\x            toggle expanded output  \timing        show query time
\i file.sql   run a SQL file          \q             quit
```
Create/drop: `CREATE DATABASE mydb;` `DROP DATABASE mydb;`

### Q94. Roles and permissions in PostgreSQL?
Users and groups are both **roles.**
```sql
CREATE ROLE app_user WITH LOGIN PASSWORD 'secret';
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT SELECT, INSERT, UPDATE ON employees TO app_user;
REVOKE DELETE ON employees FROM app_user;
```
Follow **least privilege**: the app should not use the superuser.

### Q95. What are schemas in PostgreSQL?
Namespaces inside a database to organize objects (`public`, `sales`, `hr`). Access like `sales.orders`. Controlled by `search_path`.

### Q96. What are extensions?
Add-ons that add features: **PostGIS** (geospatial), **pg_trgm** (fuzzy search/LIKE speedups), **uuid-ossp / pgcrypto**, **pg_stat_statements** (find slow queries), **hstore**, **TimescaleDB**. Install: `CREATE EXTENSION pg_trgm;`

### Q97. Full-text search in PostgreSQL?
```sql
SELECT * FROM articles
WHERE to_tsvector('english', title || ' ' || body) @@ to_tsquery('english', 'database & index');
CREATE INDEX idx_fts ON articles USING GIN (to_tsvector('english', title || ' ' || body));
```

### Q98. Table inheritance and partitioning in PostgreSQL?
Declarative partitioning:
```sql
CREATE TABLE orders (id BIGINT, created_at DATE, amount NUMERIC) PARTITION BY RANGE (created_at);
CREATE TABLE orders_2026_01 PARTITION OF orders FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');
```
Types: RANGE, LIST, HASH. Queries scan only the needed partitions (**partition pruning**).

### Q99. Replication in PostgreSQL?
- **Streaming (physical) replication:** copies WAL to standby servers (hot standby for read-only queries, failover).
- **Logical replication:** replicates selected tables/changes (publications/subscriptions); useful for upgrades and partial copies.
Tools for HA/failover: **Patroni, repmgr**, managed services (RDS, Cloud SQL, Supabase).

### Q100. What is connection pooling and PgBouncer?
Each PostgreSQL connection is a **separate process** (heavy). **PgBouncer** is a lightweight pooler that lets thousands of app clients share a small number of real DB connections.

### Q101. What is `pg_stat_statements`? How to find slow queries?
An extension that records execution stats of queries (calls, total time, mean time). Query it to find top slow queries; also use `log_min_duration_statement` and `EXPLAIN ANALYZE`.

### Q102. How to backup and restore PostgreSQL?
```bash
pg_dump -U user -d mydb -F c -f mydb.dump        # custom-format backup
pg_restore -U user -d mydb_new mydb.dump         # restore
pg_dump -U user mydb > mydb.sql                  # plain SQL
psql -U user -d mydb_new -f mydb.sql
pg_dumpall                                       # all databases + roles
```
For point-in-time recovery: base backup + WAL archiving.

### Q103. What is a sequence?
An object that generates unique numbers (`nextval('seq')`). Used behind SERIAL/IDENTITY. Gaps can occur if a transaction rolls back (that's normal).

### Q104. What are CTE materialization and `LATERAL`? (awareness)
`LATERAL` lets a subquery in FROM refer to earlier tables (useful for "top N per row"). In PostgreSQL 12+, CTEs are inlined by default unless marked `MATERIALIZED`.

### Q105. What are generated columns, `ILIKE`, `DISTINCT ON`?
```sql
SELECT DISTINCT ON (customer_id) customer_id, order_date, amount
FROM orders ORDER BY customer_id, order_date DESC;    -- latest order per customer
```
`ILIKE` = case-insensitive LIKE. Generated columns: `price_with_tax NUMERIC GENERATED ALWAYS AS (price * 1.18) STORED`.

### Q106. How to see running queries and kill a stuck query?
```sql
SELECT pid, state, query FROM pg_stat_activity WHERE state <> 'idle';
SELECT pg_cancel_backend(pid);      -- cancel the query
SELECT pg_terminate_backend(pid);   -- kill the connection
```

### Q107. How do you handle a long-running or locked query in production?
Find it in `pg_stat_activity`, check locks (`pg_locks`), cancel if safe, and fix the root cause (missing index, long transaction, bad query). Set `statement_timeout` and `idle_in_transaction_session_timeout`.

---

# Part 7 — MongoDB Basics and CRUD

### Q108. What is MongoDB?
A **NoSQL document database** that stores data as **flexible JSON-like documents (BSON)**, with built-in **replication and sharding** for scale.

### Q109. Terminology: SQL vs MongoDB?
| SQL | MongoDB |
|---|---|
| Database | Database |
| Table | **Collection** |
| Row | **Document** |
| Column | **Field** |
| Primary key | `_id` (auto-generated **ObjectId**) |
| JOIN | `$lookup` / embedding |
| Index | Index |

### Q110. What is BSON?
**Binary JSON.** MongoDB's storage format; supports more types than JSON (ObjectId, Date, Decimal128, Binary, Int32/Int64). Max document size: **16 MB.**

### Q111. What is ObjectId?
A **12-byte unique `_id`** created automatically: timestamp (4 bytes) + random value (5 bytes) + counter (3 bytes). You can get the creation time from it.

### Q112. Advantages and disadvantages of MongoDB?
**Advantages:** flexible schema, fast development, horizontal scaling (sharding), built-in replication, rich query/aggregation, good for JSON-style data.
**Disadvantages:** joins are limited/expensive, data duplication from denormalization, weaker for highly relational/transaction-heavy data (though multi-document transactions exist), memory usage.

### Q113. When to choose MongoDB over SQL?
Content/catalogs, user profiles, IoT/logs/events, real-time analytics, rapidly changing schemas, hierarchical data, very high write/scale needs. Choose SQL for strong relations, complex joins, strict consistency, and financial systems.

### Q114. Basic shell commands?
```js
show dbs
use mydb                 // switch (creates when data is inserted)
show collections
db.users.drop()
db.dropDatabase()
db.createCollection("users")
```

### Q115. Create (Insert) operations?
```js
db.users.insertOne({ name: "Ravi", age: 25, skills: ["node", "react"] });
db.users.insertMany([{ name: "A", age: 20 }, { name: "B", age: 30 }]);
```

### Q116. Read (Find) operations?
```js
db.users.find();                                  // all
db.users.find({ age: 25 });                       // filter
db.users.find({ age: { $gt: 20 } }, { name: 1, _id: 0 });   // projection
db.users.findOne({ name: "Ravi" });
db.users.find().sort({ age: -1 }).skip(10).limit(5);
db.users.countDocuments({ age: { $gte: 18 } });
db.users.distinct("city");
```

### Q117. Query operators?
- **Comparison:** `$eq, $ne, $gt, $gte, $lt, $lte, $in, $nin`
- **Logical:** `$and, $or, $not, $nor`
- **Element:** `$exists, $type`
- **Array:** `$all, $elemMatch, $size`
- **Evaluation:** `$regex, $expr, $text`
```js
db.users.find({ $or: [{ age: { $lt: 20 } }, { city: "Hyderabad" }] });
db.users.find({ skills: { $in: ["node", "mongo"] } });
db.users.find({ name: { $regex: /^R/i } });
db.orders.find({ items: { $elemMatch: { qty: { $gt: 2 }, price: { $lt: 100 } } } });
```

### Q118. Update operations and update operators?
```js
db.users.updateOne({ name: "Ravi" }, { $set: { age: 26 } });
db.users.updateMany({ city: "X" }, { $set: { active: false } });
db.users.updateOne({ name: "Ravi" }, { $inc: { age: 1 } });
db.users.updateOne({ name: "Ravi" }, { $push: { skills: "mongo" } });
db.users.updateOne({ name: "Ravi" }, { $addToSet: { skills: "node" } });   // no duplicates
db.users.updateOne({ name: "Ravi" }, { $pull: { skills: "react" } });
db.users.updateOne({ name: "Ravi" }, { $unset: { temp: "" } });
db.users.updateOne({ email: "x@x.com" }, { $set: { name: "X" } }, { upsert: true });  // insert if not found
db.users.replaceOne({ _id: id }, { name: "New" });   // replaces whole document
```
Others: `$rename, $min, $max, $mul, $pop, $currentDate`.

### Q119. `updateOne` vs `replaceOne` vs `findOneAndUpdate`?
`updateOne` modifies **specific fields** using operators. `replaceOne` **replaces the whole document** (except `_id`). `findOneAndUpdate` updates **and returns** the document (before or after, with `returnDocument: "after"`).

### Q120. Delete operations?
```js
db.users.deleteOne({ name: "Ravi" });
db.users.deleteMany({ active: false });
db.users.deleteMany({});     // all documents (collection stays)
```

### Q121. `find()` vs `findOne()`?
`find()` returns a **cursor** (many documents). `findOne()` returns **one document** (or null).

### Q122. How do you query nested fields and arrays?
Use **dot notation** (quotes required):
```js
db.users.find({ "address.city": "Hyderabad" });
db.users.find({ "skills.0": "node" });          // first element
db.users.find({ skills: "node" });              // array contains
```

### Q123. What is projection?
Choosing which fields to return: `{ name: 1, email: 1, _id: 0 }`. Reduces data transfer. (You can't mix include and exclude except for `_id`.)

### Q124. What are `bulkWrite` and `insertMany` ordering?
`bulkWrite` runs many insert/update/delete operations in **one request.** `ordered: true` (default) stops at the first error; `ordered: false` continues with the rest.

### Q125. Data validation in MongoDB?
**Schema validation** with `$jsonSchema` on the collection:
```js
db.createCollection("users", {
  validator: { $jsonSchema: {
    bsonType: "object",
    required: ["name", "email"],
    properties: { name: { bsonType: "string" }, age: { bsonType: "int", minimum: 0 } }
  } }
});
```
In apps, Mongoose adds validation at the application level.

### Q126. What is capped collection? TTL index?
**Capped collection:** fixed-size collection that overwrites oldest data (logs). **TTL index:** automatically **deletes documents after a time** (sessions, OTPs):
```js
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 });
```

---

# Part 8 — MongoDB Advanced

### Q127. What is the aggregation pipeline? (VERY IMPORTANT)
A framework to process data in **stages**; the output of one stage is the input of the next.
Common stages: `$match` (filter), `$group` (group + accumulate), `$project` (shape fields), `$sort`, `$limit`, `$skip`, `$unwind` (flatten arrays), `$lookup` (join), `$addFields`, `$count`, `$facet`, `$out/$merge`.
```js
db.orders.aggregate([
  { $match: { status: "paid" } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" }, orders: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 5 }
]);
```
**Tip:** put `$match` and `$limit` **early** to reduce data (and so indexes can be used).

### Q128. `$lookup` (join) example?
```js
db.orders.aggregate([
  { $lookup: { from: "customers", localField: "customerId", foreignField: "_id", as: "customer" } },
  { $unwind: "$customer" }
]);
```
`$lookup` is like a **LEFT OUTER JOIN.** Frequent `$lookup` use may signal the schema should embed data instead.

### Q129. `$unwind` and `$group` with arrays — example: count by tag?
```js
db.posts.aggregate([
  { $unwind: "$tags" },
  { $group: { _id: "$tags", count: { $sum: 1 } } },
  { $sort: { count: -1 } }
]);
```

### Q130. Accumulators in `$group`?
`$sum, $avg, $min, $max, $first, $last, $push` (array of all values), `$addToSet` (unique values).

### Q131. Aggregation vs `find()`?
`find()` filters and returns documents. **Aggregation** can transform, group, join, calculate, and reshape data (analytics/reporting).

### Q132. What are indexes in MongoDB? Types?
Indexes speed up queries (otherwise **COLLSCAN** = scans every document). `_id` is indexed by default.
- **Single field**, **Compound**, **Multikey** (on arrays), **Text**, **Geospatial (2dsphere)**, **Hashed** (for sharding), **TTL**, **Unique**, **Partial**, **Sparse**, **Wildcard**
```js
db.users.createIndex({ email: 1 }, { unique: true });
db.users.createIndex({ city: 1, age: -1 });
db.users.createIndex({ bio: "text" });
db.users.getIndexes();
db.users.dropIndex("email_1");
```

### Q133. How do you check if a query uses an index?
```js
db.users.find({ email: "x@x.com" }).explain("executionStats");
```
Look for **IXSCAN** (index used) vs **COLLSCAN** (full scan), and compare `totalDocsExamined` vs `nReturned` (should be close).

### Q134. What is the ESR rule for compound indexes?
Order fields as **Equality → Sort → Range.** Example: query `{ status: "A", age: { $gt: 20 } }` sorted by `name` → index `{ status: 1, name: 1, age: 1 }`.

### Q135. What is a covered query in MongoDB?
A query where all requested fields are in the **index** (projection excludes `_id` unless indexed), so MongoDB doesn't read documents.

### Q136. Embedding vs referencing? (VERY IMPORTANT schema design)
- **Embed** when data is **read together**, one-to-few, and doesn't grow unbounded (address in user; comments limited).
```js
{ _id: 1, name: "Ravi", addresses: [{ city: "Hyd", pin: "500001" }] }
```
- **Reference** when data is **large, shared, many-to-many, frequently changing, or unbounded** (orders of a customer).
```js
{ _id: 101, customerId: 1, total: 500 }
```
**Rule of thumb:** "Data that is accessed together should be stored together", but avoid unbounded arrays and the 16 MB limit.

### Q137. Schema design patterns/best practices?
Design for **your queries**, not just entities. Avoid unbounded arrays, avoid too many `$lookup`s, keep documents reasonably small, use appropriate indexes, and apply patterns like **subset** (store only recent items), **bucket** (group time-series data), **computed** (store precomputed totals), and **extended reference** (copy a few fields from the referenced doc).

### Q138. Is MongoDB schema-less?
It has a **flexible schema**, not "no schema." Documents in one collection can differ, but in real apps you should enforce structure via Mongoose or `$jsonSchema`.

### Q139. What is a replica set? 
A group of MongoDB servers holding **the same data** for **high availability and redundancy.** One **primary** (handles writes), several **secondaries** (copy via the **oplog**), optional **arbiter.** If the primary fails, an **election** picks a new primary automatically.

### Q140. What is sharding in MongoDB?
**Horizontal scaling:** splitting data across multiple servers (**shards**) using a **shard key.** Components: **shards** (data), **mongos** (query router), **config servers** (metadata). Each shard is usually a replica set.

### Q141. What is a shard key? How to choose a good one?
The field(s) that decide where a document goes. A good key has **high cardinality**, **even distribution**, and matches common query patterns. **Avoid monotonically increasing keys** (like timestamp or ObjectId) with range sharding because they create a **hot shard**; use **hashed** sharding or a compound key.

### Q142. Replication vs sharding?
Replication = **copies** of the same data (availability, read scaling). Sharding = **different parts** of the data on different servers (storage/write scaling). Used together in production.

### Q143. What are write concern, read concern and read preference?
- **Write concern:** how many nodes must acknowledge a write (`w: 1`, `w: "majority"`, `j: true` for journal). 
- **Read concern:** consistency level of reads (`local`, `majority`, `snapshot`).
- **Read preference:** where to read from (`primary`, `secondary`, `nearest`).
`w: "majority"` is the safest against data loss during failover.

### Q144. Does MongoDB support transactions?
Yes. **Single-document operations are atomic** by default. **Multi-document ACID transactions** are supported (4.0+ for replica sets, 4.2+ for sharded clusters) but add overhead, so good schema design (embedding) should reduce the need for them.
```js
const session = client.startSession();
await session.withTransaction(async () => {
  await accounts.updateOne({ _id: 1 }, { $inc: { balance: -500 } }, { session });
  await accounts.updateOne({ _id: 2 }, { $inc: { balance: 500 } }, { session });
});
```

### Q145. What storage engine does MongoDB use? What is journaling?
**WiredTiger** (default): document-level concurrency, compression, checkpoints. **Journaling** writes operations to a log first for crash recovery (similar to WAL).

### Q146. Mongo shell vs Compass vs Atlas?
**mongosh:** command-line shell. **Compass:** GUI to explore data/indexes/explain plans. **Atlas:** MongoDB's managed cloud service.

### Q147. How do you improve MongoDB performance?
Proper **indexes** (check with `explain`), **projection**, **limit/pagination**, avoid large `skip`, avoid unbounded arrays, **schema design for queries**, use the **aggregation pipeline efficiently** (`$match` early), **lean reads**, sufficient RAM for the working set, replica set for read scaling, shard when needed, monitor with the **profiler** (`db.setProfilingLevel`) and `$indexStats`.

### Q148. What are MongoDB backup methods?
`mongodump` / `mongorestore` (logical), filesystem snapshots, **Atlas continuous backups / point-in-time restore**, and oplog-based recovery.

### Q149. What are Change Streams?
Let applications **listen to real-time changes** (insert/update/delete) in a collection (requires replica set). Used for notifications, syncing, event-driven apps.

### Q150. MongoDB vs PostgreSQL — final comparison?
| Point | MongoDB | PostgreSQL |
|---|---|---|
| Model | Documents | Tables (plus JSONB) |
| Schema | Flexible | Strict (flexible via JSONB) |
| Joins | Limited (`$lookup`) | Strong |
| Transactions | Supported, heavier | Strong, core feature |
| Scaling | Built-in sharding | Vertical + replicas; sharding via extensions (e.g., Citus) |
| Best for | Changing/hierarchical data, high scale | Relational data, analytics, strong consistency |
**Smart answer:** "Choose based on the data and queries. PostgreSQL with JSONB covers many cases; MongoDB shines for flexible documents and built-in horizontal scale."

---

# Part 9 — SQL Query Practice

> These are the **most common live SQL questions.** Practice writing them without looking.

**Sample tables used below**
```sql
employees(id, name, email, salary, dept_id, manager_id, hired_on)
departments(id, dept_name)
customers(id, name, city)
orders(id, customer_id, order_date, amount)
```

### Q151. Find the 2nd highest salary.
```sql
-- Method 1: subquery (works everywhere)
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: DENSE_RANK (best, easy for Nth)
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk FROM employees
) t WHERE rnk = 2;

-- Method 3: LIMIT/OFFSET (PostgreSQL/MySQL)
SELECT DISTINCT salary FROM employees ORDER BY salary DESC LIMIT 1 OFFSET 1;
```

### Q152. Find the Nth highest salary (e.g., N = 3).
```sql
SELECT DISTINCT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk FROM employees
) t WHERE rnk = 3;
```

### Q153. Find duplicate emails.
```sql
SELECT email, COUNT(*) FROM employees GROUP BY email HAVING COUNT(*) > 1;
```

### Q154. Delete duplicate rows, keeping the one with the lowest id.
```sql
-- PostgreSQL
DELETE FROM employees a USING employees b
WHERE a.id > b.id AND a.email = b.email;

-- Works in many DBs (careful in MySQL: needs a derived table)
DELETE FROM employees
WHERE id NOT IN (SELECT MIN(id) FROM employees GROUP BY email);
```

### Q155. Highest salary in each department (with employee name).
```sql
SELECT e.name, e.dept_id, e.salary
FROM employees e
WHERE e.salary = (SELECT MAX(salary) FROM employees WHERE dept_id = e.dept_id);

-- or with window function
SELECT * FROM (
  SELECT name, dept_id, salary,
         RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
  FROM employees
) t WHERE rnk = 1;
```

### Q156. Departments with more than 5 employees.
```sql
SELECT d.dept_name, COUNT(e.id) AS total
FROM departments d JOIN employees e ON e.dept_id = d.id
GROUP BY d.dept_name HAVING COUNT(e.id) > 5;
```

### Q157. Employees who earn more than their manager.
```sql
SELECT e.name FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

### Q158. Departments with no employees.
```sql
SELECT d.dept_name FROM departments d
LEFT JOIN employees e ON e.dept_id = d.id
WHERE e.id IS NULL;
```

### Q159. Customers who never placed an order.
```sql
SELECT c.name FROM customers c
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);
-- or: LEFT JOIN orders o ON ... WHERE o.id IS NULL
```

### Q160. Top 3 highest-paid employees in each department.
(See Q46 — `DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC)` with `rnk <= 3`.)

### Q161. Running total of orders by date.
```sql
SELECT order_date, amount, SUM(amount) OVER (ORDER BY order_date) AS running_total FROM orders;
```

### Q162. Monthly sales total.
```sql
SELECT DATE_TRUNC('month', order_date) AS month, SUM(amount) AS total
FROM orders GROUP BY 1 ORDER BY 1;
```

### Q163. Latest order per customer.
```sql
SELECT DISTINCT ON (customer_id) customer_id, order_date, amount     -- PostgreSQL
FROM orders ORDER BY customer_id, order_date DESC;

-- Portable version
SELECT * FROM (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rn FROM orders
) t WHERE rn = 1;
```

### Q164. Customers with total spend above the average customer spend.
```sql
WITH spend AS (SELECT customer_id, SUM(amount) AS total FROM orders GROUP BY customer_id)
SELECT customer_id, total FROM spend WHERE total > (SELECT AVG(total) FROM spend);
```

### Q165. Find employees hired in the last 30 days.
```sql
SELECT * FROM employees WHERE hired_on >= CURRENT_DATE - INTERVAL '30 days';
```

### Q166. Find employees whose name starts with 'A' and salary between 30k and 60k.
```sql
SELECT * FROM employees WHERE name LIKE 'A%' AND salary BETWEEN 30000 AND 60000;
```

### Q167. Get the 5 most recent orders (pagination page 2, 10 per page).
```sql
SELECT * FROM orders ORDER BY order_date DESC LIMIT 10 OFFSET 10;
```

### Q168. Swap values with CASE (e.g., change 'M' to 'F' and 'F' to 'M').
```sql
UPDATE person SET gender = CASE WHEN gender = 'M' THEN 'F' ELSE 'M' END;
```

### Q169. Find employees with the same salary.
```sql
SELECT salary, COUNT(*) FROM employees GROUP BY salary HAVING COUNT(*) > 1;
```

### Q170. Compare today's and yesterday's value (LAG).
```sql
SELECT order_date, amount,
       amount - LAG(amount) OVER (ORDER BY order_date) AS change
FROM daily_sales;
```

### Q171. Find the median salary (PostgreSQL).
```sql
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) FROM employees;
```

### Q172. Pivot-like query (count by status using CASE).
```sql
SELECT
  COUNT(*) FILTER (WHERE status = 'paid')    AS paid,      -- PostgreSQL FILTER
  SUM(CASE WHEN status = 'failed' THEN 1 ELSE 0 END) AS failed   -- portable
FROM orders;
```

### Q173. Department-wise total salary, only departments above 100,000, highest first.
```sql
SELECT dept_id, SUM(salary) AS total FROM employees
GROUP BY dept_id HAVING SUM(salary) > 100000 ORDER BY total DESC;
```

### Q174. Find consecutive duplicates / gaps (concept).
Use `LAG()`/`LEAD()` or `ROW_NUMBER()` differences. Mention the approach even if you can't write it fully.

### Q175. Equivalent MongoDB queries for the same ideas?
```js
// Duplicate emails
db.users.aggregate([{ $group: { _id: "$email", c: { $sum: 1 } } }, { $match: { c: { $gt: 1 } } }]);

// Total spend per customer, highest first
db.orders.aggregate([{ $group: { _id: "$customerId", total: { $sum: "$amount" } } }, { $sort: { total: -1 } }]);

// 2nd highest salary
db.employees.find().sort({ salary: -1 }).skip(1).limit(1);

// Employees per department
db.employees.aggregate([{ $group: { _id: "$deptId", count: { $sum: 1 } } }]);
```

---

# Part 10 — Using Databases with Node.js

### Q176. How do you connect PostgreSQL to Node.js?
Using the **`pg`** library with a **connection pool.**
```js
const { Pool } = require("pg");
const pool = new Pool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  port: 5432,
  max: 10,
});

// Parameterized query (prevents SQL injection)
const { rows } = await pool.query("SELECT * FROM users WHERE id = $1", [id]);

// Insert with RETURNING
const result = await pool.query(
  "INSERT INTO users (name, email) VALUES ($1, $2) RETURNING *", [name, email]
);
console.log(result.rows[0]);
```
PostgreSQL uses `$1, $2` placeholders (MySQL uses `?`).

### Q177. How to do a transaction in Node.js with `pg`?
```js
const client = await pool.connect();
try {
  await client.query("BEGIN");
  await client.query("UPDATE accounts SET balance = balance - $1 WHERE id = $2", [500, 1]);
  await client.query("UPDATE accounts SET balance = balance + $1 WHERE id = $2", [500, 2]);
  await client.query("COMMIT");
} catch (err) {
  await client.query("ROLLBACK");
  throw err;
} finally {
  client.release();     // ALWAYS release the client back to the pool
}
```
Use the **same client** for all statements of the transaction (not `pool.query`).

### Q178. How do you connect MongoDB in Node.js (native driver and Mongoose)?
```js
// Native driver
const { MongoClient } = require("mongodb");
const client = new MongoClient(process.env.MONGO_URI);
await client.connect();
const db = client.db("mydb");
const users = await db.collection("users").find({ age: { $gt: 20 } }).toArray();

// Mongoose
const mongoose = require("mongoose");
await mongoose.connect(process.env.MONGO_URI);
```

### Q179. Mongoose schema with validation, index and relationship?
```js
const orderSchema = new mongoose.Schema({
  user:   { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true, index: true },
  items:  [{ product: String, qty: { type: Number, min: 1 } }],
  total:  { type: Number, required: true },
  status: { type: String, enum: ["pending", "paid", "shipped"], default: "pending" },
}, { timestamps: true });

const Order = mongoose.model("Order", orderSchema);
const orders = await Order.find({ status: "paid" }).populate("user", "name email").lean();
```

### Q180. Which ORMs/query builders are used with SQL in Node?
**Prisma, Sequelize, TypeORM, Knex.js** (query builder), Drizzle. They give models, migrations and safe queries. Mention what you've used.

### Q181. How do you prevent SQL/NoSQL injection in Node.js?
- SQL: **parameterized queries** (`$1`) or ORM; never concatenate strings.
- MongoDB: validate input types (Joi/Zod), use `express-mongo-sanitize`, don't pass `req.body` directly into queries (blocks `$ne`, `$gt` operator injection).

### Q182. How do you handle DB errors in an API?
Catch errors, map them to correct HTTP codes: unique violation (PG code **23505**, Mongo **11000**) → **409 Conflict**; foreign key violation (PG **23503**) → 400/409; not found → **404**; validation → **400/422**; other → **500** (don't expose internal details).

### Q183. How do you keep database credentials safe?
Environment variables / secret managers, never in Git, a **least-privilege DB user** for the app, SSL/TLS connection, restrict network access (IP allowlist/VPC).

### Q184. How do you manage schema changes in a Node project?
Use **migrations** (Knex, Prisma Migrate, Sequelize CLI, node-pg-migrate) in version control, run them in CI/CD, and make changes **backward compatible** (add column → deploy code → remove old column later).

### Q185. How do you seed and test with a database?
Seed scripts for initial data; for tests use a **separate test database**, **Docker containers (Testcontainers)**, or `mongodb-memory-server`; clean data between tests.

---

# Part 11 — Scenario-Based and HR Questions

### Q186. A query that was fast is now slow. What do you check?
Data growth; missing/unused **index**; outdated statistics (`ANALYZE`); changed query plan (`EXPLAIN ANALYZE`); table bloat (VACUUM); lock contention/long transactions; sudden traffic; a recent code/ORM change causing N+1 queries.

### Q187. How would you design the database for an e-commerce app?
Tables: `users, addresses, products, categories, product_variants, carts, cart_items, orders, order_items, payments, reviews, inventory`. Relationships: user → orders (1-N), orders → order_items (1-N), products ↔ categories (N-N via junction). Key points: **store price at order time** in `order_items`, use **transactions** for checkout/stock update, **indexes** on foreign keys/search columns, store money as **NUMERIC**, and consider **MongoDB/Elasticsearch/Redis** for catalog search and caching.

### Q188. How do you design a many-to-many relationship?
Use a **junction table** with two foreign keys (and a composite primary key): `student_courses(student_id, course_id, enrolled_on, PRIMARY KEY(student_id, course_id))`. In MongoDB: array of references on one or both sides (or a separate collection if the relation has its own data).

### Q189. How would you handle a table with 100 million rows?
Proper **indexes**, **partitioning**, **archive old data**, keyset pagination, avoid `SELECT *`, **read replicas**, caching, materialized views for reports, and **sharding** at very large scale.

### Q190. How do you prevent two users from buying the last item (race condition)?
Use a **transaction with row lock** (`SELECT ... FOR UPDATE`) or an **atomic conditional update:** `UPDATE products SET stock = stock - 1 WHERE id = $1 AND stock > 0` (check affected rows). In MongoDB: `updateOne({ _id, stock: { $gt: 0 } }, { $inc: { stock: -1 } })`, which is atomic on a single document.

### Q191. When would you pick PostgreSQL vs MongoDB for a new project?
Ask: Is data **relational** with complex joins/transactions? → **PostgreSQL.** Is the data **document-like, flexible, or very large-scale with simple access patterns?** → **MongoDB.** Many teams pick PostgreSQL by default (it also handles JSON) and add MongoDB/Redis where the use case fits.

### Q192. How do you migrate data from one database to another with minimal downtime?
Plan schema mapping, **copy data in batches** (or use replication/CDC tools), keep source and target in sync, test thoroughly, do a **short cutover window**, keep a rollback plan, and verify counts/checksums.

### Q193. How do you ensure data integrity?
Constraints (PK, FK, UNIQUE, CHECK, NOT NULL), transactions, validation at app and DB level, normalization, backups, proper isolation levels, and monitoring.

### Q194. Tell me about your database experience / project.
Template: "In my project I used **PostgreSQL/MongoDB** for ___. I designed tables/collections like ___, wrote queries with **joins and aggregations**, added **indexes** for slow queries, used **transactions** for ___, and connected it to Node.js using **pg / Prisma / Mongoose.** I used **EXPLAIN** to optimize a slow query."

### Q195. What challenge did you face with databases?
Examples: slow list API fixed by adding an index and pagination; N+1 queries fixed with JOIN/populate; duplicate records fixed with a unique constraint; deadlock/race condition fixed with transaction and consistent lock order.

### Q196. Strengths/weakness/why hire you?
- **Strength:** good SQL fundamentals, I check query plans before and after optimization.
- **Weakness:** "I'm still learning advanced topics like sharding and replication in depth; I practice using local setups."
- **Why hire me:** "I understand relational design, can write and optimize queries, and I know when to use SQL vs NoSQL."

### Q197. Questions to ask the interviewer?
- "Which databases and ORMs does the team use, and how do you handle migrations?"
- "How do you monitor and optimize slow queries in production?"

---

# Part 12 — Last-Minute Cheat Sheet

### One-line answers (memorize)
| Topic | One-line answer |
|---|---|
| DBMS / RDBMS | Software to manage data / DBMS using tables and relations |
| SQL vs NoSQL | Structured + ACID + joins vs flexible + horizontal scale |
| ACID | Atomicity, Consistency, Isolation, Durability |
| CAP | Pick 2 of Consistency, Availability, Partition tolerance |
| Primary key | Unique, not null, identifies a row |
| Foreign key | Links to another table's primary key |
| DELETE / TRUNCATE / DROP | Remove rows (WHERE) / remove all rows / remove table |
| WHERE vs HAVING | Filter rows before grouping / filter groups after |
| INNER vs LEFT JOIN | Only matches / all left rows + matches |
| UNION vs UNION ALL | Removes duplicates / keeps duplicates |
| IN vs EXISTS | Compare to list / check any row exists (use NOT EXISTS over NOT IN) |
| RANK vs DENSE_RANK | Gaps after ties / no gaps |
| Window function | Calculates across related rows without collapsing them |
| CTE | Named temporary result using WITH |
| Index | Structure that speeds up reads, slows writes |
| Clustered index | Physical order of data (one per table) |
| Normalization | Reduce redundancy: 1NF atomic, 2NF whole key, 3NF only key |
| Transaction | Group of statements: all or nothing |
| Isolation levels | Read Uncommitted < Read Committed < Repeatable Read < Serializable |
| Deadlock | Two transactions waiting on each other's locks |
| EXPLAIN ANALYZE | Shows the real execution plan and timing |
| PostgreSQL MVCC | Multiple row versions; readers don't block writers |
| VACUUM | Cleans dead tuples left by MVCC |
| JSONB | Binary JSON in PostgreSQL, indexable with GIN |
| MongoDB document | JSON-like BSON record (max 16 MB) |
| Aggregation pipeline | Multi-stage data processing ($match, $group, $lookup...) |
| Embed vs reference | Together-read small data vs large/shared/unbounded data |
| Replica set | Primary + secondaries for high availability |
| Sharding | Splitting data across servers by shard key |
| Write concern | How many nodes must confirm a write |

### Top 20 most-asked database questions
1. SQL vs NoSQL; when to use which
2. ACID properties
3. Primary key vs foreign key vs unique key
4. DELETE vs TRUNCATE vs DROP
5. WHERE vs HAVING; GROUP BY
6. Types of JOINs with examples
7. Subquery vs JOIN; IN vs EXISTS
8. Second/Nth highest salary query
9. Find duplicates and delete them
10. Window functions (ROW_NUMBER, RANK, DENSE_RANK)
11. Indexes: how they work, when to use, clustered vs non-clustered
12. Normalization (1NF–3NF)
13. Transactions and isolation levels
14. How to optimize a slow query (EXPLAIN)
15. PostgreSQL: MVCC, JSONB, VACUUM, index types
16. MongoDB CRUD and operators
17. MongoDB aggregation pipeline
18. Embedding vs referencing in MongoDB
19. Replication vs sharding
20. SQL injection and how Node.js apps prevent it

### Interview day tips
- **Think aloud** when writing queries: first state the tables and the plan (join → filter → group → sort).
- Test your query mentally with a **small sample** and mention **edge cases** (NULLs, ties, empty results).
- For optimization questions, always say: **"I'd check EXPLAIN ANALYZE, then indexes, then the query shape."**
- For SQL vs MongoDB questions, **avoid saying one is always better.** Explain trade-offs.
- Practice the **Part 9 queries** by typing them in a real PostgreSQL database (create the sample tables).
- If you don't know: *"I haven't used it directly, but I understand it as ... and I'm eager to learn."*
- Stay calm and confident. **You've prepared well.** 💪

---
**Best of luck with your interview! 🚀**
