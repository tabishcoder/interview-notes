# DBMS & SQL Interview Study Guide
### For Fresh Graduate & Early-Career Software Engineering Interviews

---

> **How to Use This Guide**
> - Read each section once to understand concepts.
> - Review the interview questions and key takeaways before interviews.
> - Use the Final Revision Cheat Sheet for last-minute review.
> - Practice the Top 25 SQL Problems on paper or in a live SQL editor.

---

## Table of Contents

1. [Database Fundamentals](#1-database-fundamentals)
2. [Relational Database Concepts](#2-relational-database-concepts)
3. [SQL Fundamentals](#3-sql-fundamentals)
4. [SQL Queries](#4-sql-queries)
5. [SQL Joins](#5-sql-joins)
6. [Advanced SQL](#6-advanced-sql)
7. [Normalization](#7-normalization)
8. [Indexing](#8-indexing)
9. [Transactions](#9-transactions)
10. [Concurrency Control](#10-concurrency-control)
11. [Locks](#11-locks)
12. [Database Design](#12-database-design)
13. [Query Optimization](#13-query-optimization)
14. [Stored Procedures, Views, and Triggers](#14-stored-procedures-views-and-triggers)
15. [NoSQL Basics](#15-nosql-basics)
16. [Common Interview Questions](#16-common-interview-questions)
17. [Top 25 SQL Interview Problems](#17-top-25-sql-interview-problems)
18. [Database Design Scenarios](#18-database-design-scenarios)
19. [Performance Tuning Checklist](#19-performance-tuning-checklist)
20. [Final Revision Cheat Sheet](#20-final-revision-cheat-sheet)

---

## 1. Database Fundamentals

### What is a Database?

A **database** is an organized collection of structured data stored electronically so it can be accessed, managed, and updated efficiently.

### What is a DBMS?

A **Database Management System (DBMS)** is software that acts as an interface between users/applications and the database. It manages how data is stored, retrieved, and secured.

**Examples:** MySQL, PostgreSQL, Oracle, SQL Server, SQLite

### Why is DBMS Needed?

Without a DBMS, storing data in flat files creates severe problems:

| Problem Without DBMS | How DBMS Solves It |
|---|---|
| Data redundancy (same data stored multiple times) | Centralized storage with normalization |
| Data inconsistency (conflicting values) | Integrity constraints enforce consistency |
| Difficult data access (manual file parsing) | Query language (SQL) for easy retrieval |
| No security control | Role-based access control |
| No concurrent access | Transaction management and locking |
| No backup/recovery | Built-in backup and recovery mechanisms |

### Features of DBMS

- **Data Abstraction** - Hides internal complexity from users
- **Data Independence** - Changes to storage don't affect applications
- **Data Integrity** - Enforces rules to keep data accurate
- **Data Security** - Controls who can access what
- **Concurrency Control** - Handles multiple users accessing data simultaneously
- **Backup and Recovery** - Protects against data loss
- **Query Language** - SQL for data manipulation

### DBMS vs File System

| Feature | File System | DBMS |
|---|---|---|
| Data redundancy | High | Low (normalized) |
| Data consistency | Poor | Enforced by constraints |
| Security | Basic OS-level | Fine-grained access control |
| Concurrent access | Not supported well | Managed via transactions |
| Query capability | Manual programming | SQL |
| Data independence | None | Logical and physical |
| Recovery | Manual | Automatic with logs |
| Relationships | Not supported | Foreign keys, joins |

### Types of Databases

| Type | Description | Examples |
|---|---|---|
| **Relational (RDBMS)** | Data in tables with relationships | MySQL, PostgreSQL, Oracle |
| **Document** | Data as JSON/BSON documents | MongoDB, CouchDB |
| **Key-Value** | Simple key → value pairs | Redis, DynamoDB |
| **Column-Family** | Data stored by column groups | Cassandra, HBase |
| **Graph** | Data as nodes and edges | Neo4j, Amazon Neptune |
| **Time-Series** | Optimized for time-stamped data | InfluxDB, TimescaleDB |
| **Search Engines** | Full-text search optimized | Elasticsearch, Solr |
| **In-Memory** | Data stored in RAM for speed | Redis, Memcached |

### Interview Questions - Database Fundamentals

**Q: What is the difference between a database and a DBMS?**
> A database is the actual collection of data. A DBMS is the software that manages that data. Think of the database as a library and the DBMS as the librarian.

**Q: Why would you choose a DBMS over a flat file?**
> DBMS provides data integrity, concurrent access, security, easy querying via SQL, and automatic backup/recovery — none of which flat files offer out of the box.

**Q: What is data independence?**
> Data independence means you can change how data is physically stored without changing the application that uses it (physical independence), or change the logical schema without affecting external views (logical independence).

---

> **Key Takeaways - Section 1**
> - DBMS solves the core problems of flat file storage: redundancy, inconsistency, poor security, and no concurrency.
> - Relational databases (RDBMS) are the most common type in interviews.
> - Know the difference between a database (data) and DBMS (software managing the data).

---

## 2. Relational Database Concepts

### Tables, Rows, and Columns

A **relational database** organizes data into **tables** (also called relations).

- **Table (Relation)** - A collection of related data organized in rows and columns
- **Row (Tuple/Record)** - A single data entry in a table
- **Column (Attribute/Field)** - A specific property of the data

```
employees table:
+----+----------+-----------+--------+
| id | name     | dept_id   | salary |
+----+----------+-----------+--------+
|  1 | Alice    |    10     | 80000  |
|  2 | Bob      |    20     | 90000  |
|  3 | Carol    |    10     | 75000  |
+----+----------+-----------+--------+
```

### Types of Keys

Keys uniquely identify rows and establish relationships between tables.

#### Primary Key

- **Definition:** A column (or set of columns) that **uniquely identifies every row** in a table.
- **Rules:** Must be unique, must NOT be NULL, only one primary key per table.
- **Real-world use:** `employee_id`, `order_id`, `user_id`

```sql
CREATE TABLE employees (
    id       INT PRIMARY KEY,
    name     VARCHAR(100) NOT NULL,
    salary   DECIMAL(10,2)
);
```

#### Foreign Key

- **Definition:** A column in one table that **references the primary key** of another table.
- **Purpose:** Enforces referential integrity — prevents orphaned records.
- **Real-world use:** `orders.customer_id` references `customers.id`

```sql
CREATE TABLE orders (
    order_id    INT PRIMARY KEY,
    customer_id INT,
    amount      DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

**What happens on delete/update?**
- `CASCADE` - Delete/update child rows automatically
- `SET NULL` - Set foreign key to NULL
- `RESTRICT` - Prevent delete/update if child rows exist

#### Candidate Key

- **Definition:** Any column (or set of columns) that **could serve as a primary key** — unique and not null.
- A table can have multiple candidate keys; one becomes the primary key.
- **Example:** In a `users` table, both `user_id` and `email` can uniquely identify a user. Both are candidate keys.

#### Super Key

- **Definition:** Any set of columns that **uniquely identifies a row**, including columns beyond the minimum needed.
- Every candidate key is a super key, but not every super key is a candidate key.
- **Example:** `{user_id}`, `{email}`, `{user_id, email}`, `{user_id, name}` are all super keys.

#### Composite Key

- **Definition:** A primary key made of **two or more columns** combined.
- Used when no single column is unique enough.
- **Example:** An `enrollments` table where `(student_id, course_id)` together identify a record.

```sql
CREATE TABLE enrollments (
    student_id INT,
    course_id  INT,
    grade      CHAR(2),
    PRIMARY KEY (student_id, course_id)
);
```

#### Alternate Key

- **Definition:** A candidate key that was **not chosen** as the primary key.
- **Example:** If `user_id` is the primary key, then `email` (also unique and not null) is the alternate key.

### Key Summary Table

| Key Type | Unique | Not Null | Multiple Allowed | Description |
|---|---|---|---|---|
| Primary Key | Yes | Yes | No (only 1) | The chosen unique identifier |
| Foreign Key | No | Optional | Yes | References another table's PK |
| Candidate Key | Yes | Yes | Yes | Any potential primary key |
| Super Key | Yes | Optional | Yes | Any unique column combo |
| Composite Key | Yes (together) | Yes | No | PK from multiple columns |
| Alternate Key | Yes | Yes | Yes | Candidate key not chosen as PK |

### Interview Questions - Relational Concepts

**Q: What is the difference between a primary key and a unique key?**
> A primary key cannot be NULL and there can only be one per table. A unique key can be NULL (in most databases) and a table can have multiple unique keys.

**Q: Can a foreign key reference a non-primary key column?**
> Yes, a foreign key can reference any column with a UNIQUE constraint, not just the primary key. However, referencing the primary key is the most common practice.

**Q: What is referential integrity?**
> Referential integrity means that every foreign key value must either match a value in the referenced table or be NULL. The database enforces this automatically with foreign key constraints.

**Q: What is the difference between a candidate key and a super key?**
> A candidate key is the minimal set of columns needed for uniqueness (no extra columns). A super key may include extra columns beyond what's needed. All candidate keys are super keys, but not vice versa.

---

> **Key Takeaways - Section 2**
> - Primary key = unique + not null + only one per table.
> - Foreign key = links two tables and enforces referential integrity.
> - Candidate key = any column(s) that could be a primary key.
> - Composite key = a primary key using multiple columns.

---

## 3. SQL Fundamentals

SQL (Structured Query Language) is divided into five sub-languages based on the type of operation performed.

### DDL - Data Definition Language

Defines and modifies the **structure** of database objects (tables, indexes, views).

| Command | Purpose |
|---|---|
| `CREATE` | Create a new table, view, or index |
| `ALTER` | Modify an existing table structure |
| `DROP` | Delete a table or database permanently |
| `TRUNCATE` | Remove all rows from a table (faster than DELETE) |
| `RENAME` | Rename a table or column |

```sql
-- CREATE a table
CREATE TABLE departments (
    dept_id   INT PRIMARY KEY,
    dept_name VARCHAR(100) NOT NULL,
    location  VARCHAR(100)
);

-- ALTER: add a new column
ALTER TABLE employees ADD COLUMN email VARCHAR(255);

-- ALTER: modify column type
ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12,2);

-- TRUNCATE: remove all rows but keep structure
TRUNCATE TABLE audit_logs;

-- DROP: permanently delete the table
DROP TABLE old_archive;
```

**DDL vs DML for deletion:**
- `TRUNCATE` - Removes all rows, cannot be rolled back (DDL), very fast
- `DELETE` - Removes specific rows, can be rolled back (DML), slower

### DML - Data Manipulation Language

Manipulates the **data inside tables**.

| Command | Purpose |
|---|---|
| `INSERT` | Add new rows |
| `UPDATE` | Modify existing rows |
| `DELETE` | Remove specific rows |
| `MERGE` | Insert or update based on condition (upsert) |

```sql
-- INSERT single row
INSERT INTO employees (id, name, dept_id, salary)
VALUES (1, 'Alice', 10, 80000);

-- INSERT multiple rows
INSERT INTO employees (id, name, dept_id, salary)
VALUES (2, 'Bob', 20, 90000),
       (3, 'Carol', 10, 75000);

-- UPDATE specific rows
UPDATE employees
SET salary = salary * 1.10
WHERE dept_id = 10;

-- DELETE specific rows
DELETE FROM employees
WHERE salary < 50000;

-- MERGE (upsert) example
MERGE INTO employees AS target
USING new_hires AS source
ON target.id = source.id
WHEN MATCHED THEN
    UPDATE SET target.salary = source.salary
WHEN NOT MATCHED THEN
    INSERT (id, name, salary) VALUES (source.id, source.name, source.salary);
```

### DQL - Data Query Language

Used to **retrieve data** from the database.

| Command | Purpose |
|---|---|
| `SELECT` | Retrieve data from one or more tables |

```sql
-- Basic SELECT
SELECT name, salary FROM employees;

-- SELECT with condition
SELECT name, salary
FROM employees
WHERE dept_id = 10
ORDER BY salary DESC;

-- SELECT with aggregation
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 70000;
```

### DCL - Data Control Language

Controls **access permissions** to the database.

| Command | Purpose |
|---|---|
| `GRANT` | Give a user permission |
| `REVOKE` | Remove a user's permission |

```sql
-- Grant SELECT and INSERT on employees to user 'john'
GRANT SELECT, INSERT ON employees TO 'john'@'localhost';

-- Grant all privileges on a database
GRANT ALL PRIVILEGES ON company_db.* TO 'admin'@'localhost';

-- Revoke INSERT permission from john
REVOKE INSERT ON employees FROM 'john'@'localhost';
```

### TCL - Transaction Control Language

Controls **transactions** to ensure data consistency.

| Command | Purpose |
|---|---|
| `COMMIT` | Save all changes made in the transaction |
| `ROLLBACK` | Undo changes made in the transaction |
| `SAVEPOINT` | Create a checkpoint within a transaction |
| `SET TRANSACTION` | Set transaction properties (isolation level) |

```sql
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

-- If everything is correct:
COMMIT;

-- If something went wrong:
ROLLBACK;

-- Using savepoints
BEGIN TRANSACTION;
INSERT INTO orders (order_id, amount) VALUES (101, 250);
SAVEPOINT sp1;

INSERT INTO order_items (order_id, product_id, qty) VALUES (101, 5, 2);
SAVEPOINT sp2;

-- Rollback only to sp1 (undo order_items insert but keep orders insert)
ROLLBACK TO SAVEPOINT sp1;

COMMIT;
```

### SQL Sub-Language Summary

| Category | Commands | Auto-commit | Rollback Possible |
|---|---|---|---|
| DDL | CREATE, ALTER, DROP, TRUNCATE | Yes | No (usually) |
| DML | INSERT, UPDATE, DELETE, MERGE | No | Yes |
| DQL | SELECT | N/A | N/A |
| DCL | GRANT, REVOKE | Yes | No |
| TCL | COMMIT, ROLLBACK, SAVEPOINT | N/A | N/A |

### Interview Questions - SQL Fundamentals

**Q: What is the difference between DELETE and TRUNCATE?**
> `DELETE` is DML — it removes specific rows (with WHERE), can be rolled back, and fires triggers. `TRUNCATE` is DDL — it removes ALL rows, cannot be rolled back (in most databases), does not fire row-level triggers, and is much faster because it doesn't log individual row deletions.

**Q: What is the difference between DROP and TRUNCATE?**
> `DROP` deletes the entire table structure and data permanently. `TRUNCATE` removes all rows but keeps the table structure intact, ready for new data.

**Q: Can DDL statements be rolled back?**
> In most databases (MySQL, SQL Server), DDL statements auto-commit and cannot be rolled back. In PostgreSQL, DDL statements can be rolled back within a transaction.

---

> **Key Takeaways - Section 3**
> - DDL = structure (CREATE, ALTER, DROP), DML = data (INSERT, UPDATE, DELETE), DQL = query (SELECT).
> - DELETE is rollback-able; TRUNCATE is not (in most databases).
> - TCL commands (COMMIT, ROLLBACK) control transaction boundaries.

---

## 4. SQL Queries

### Sample Tables Used Throughout This Section

```sql
-- employees table
+----+----------+---------+--------+------------+
| id | name     | dept_id | salary | hire_date  |
+----+----------+---------+--------+------------+
|  1 | Alice    |    10   | 80000  | 2020-03-15 |
|  2 | Bob      |    20   | 90000  | 2019-07-01 |
|  3 | Carol    |    10   | 75000  | 2021-01-10 |
|  4 | David    |    30   | 60000  | 2022-05-20 |
|  5 | Eve      |    20   | 95000  | 2018-11-30 |
+----+----------+---------+--------+------------+

-- departments table
+---------+-----------+----------+
| dept_id | dept_name | budget   |
+---------+-----------+----------+
|    10   | Engineering | 500000 |
|    20   | Marketing   | 300000 |
|    30   | HR          | 200000 |
+---------+-----------+----------+
```

### SELECT

The most fundamental SQL command — retrieves data from one or more tables.

```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT name, salary FROM employees;

-- Select with expressions
SELECT name, salary, salary * 1.1 AS salary_with_bonus
FROM employees;

-- Select with column aliases
SELECT name AS employee_name, dept_id AS department
FROM employees;
```

### WHERE

Filters rows based on a condition.

```sql
-- Basic comparison
SELECT * FROM employees WHERE salary > 80000;

-- Multiple conditions with AND / OR
SELECT * FROM employees
WHERE dept_id = 10 AND salary > 70000;

SELECT * FROM employees
WHERE dept_id = 10 OR dept_id = 20;

-- NOT operator
SELECT * FROM employees WHERE NOT dept_id = 30;

-- BETWEEN (inclusive on both ends)
SELECT * FROM employees WHERE salary BETWEEN 70000 AND 90000;

-- IN operator
SELECT * FROM employees WHERE dept_id IN (10, 20);

-- LIKE operator (pattern matching)
SELECT * FROM employees WHERE name LIKE 'A%';   -- starts with A
SELECT * FROM employees WHERE name LIKE '%ob';  -- ends with ob
SELECT * FROM employees WHERE name LIKE '_a%';  -- second character is 'a'

-- IS NULL / IS NOT NULL
SELECT * FROM employees WHERE email IS NULL;
```

**LIKE Wildcards:**
- `%` - matches any sequence of characters (zero or more)
- `_` - matches exactly one character

### ORDER BY

Sorts the result set.

```sql
-- Ascending (default)
SELECT * FROM employees ORDER BY salary;
SELECT * FROM employees ORDER BY salary ASC;

-- Descending
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple columns: sort by dept first, then salary within each dept
SELECT * FROM employees
ORDER BY dept_id ASC, salary DESC;

-- Order by column position (less readable, avoid in production)
SELECT name, salary FROM employees ORDER BY 2 DESC;
```

### GROUP BY

Groups rows with the same values so aggregate functions can be applied.

```sql
-- Count employees per department
SELECT dept_id, COUNT(*) AS employee_count
FROM employees
GROUP BY dept_id;

-- Average salary per department
SELECT dept_id, AVG(salary) AS avg_salary, MAX(salary) AS max_salary
FROM employees
GROUP BY dept_id;

-- Group by multiple columns
SELECT dept_id, YEAR(hire_date) AS hire_year, COUNT(*) AS hires
FROM employees
GROUP BY dept_id, YEAR(hire_date);
```

**Common Aggregate Functions:**
| Function | Description |
|---|---|
| `COUNT(*)` | Count all rows |
| `COUNT(col)` | Count non-NULL values |
| `SUM(col)` | Sum of values |
| `AVG(col)` | Average of values |
| `MAX(col)` | Maximum value |
| `MIN(col)` | Minimum value |

### HAVING

Filters **groups** after GROUP BY (like WHERE, but for aggregated data).

```sql
-- Only show departments with more than 1 employee
SELECT dept_id, COUNT(*) AS employee_count
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 1;

-- Departments where average salary exceeds 80000
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 80000;
```

**WHERE vs HAVING:**
- `WHERE` filters **rows before** grouping
- `HAVING` filters **groups after** grouping

```sql
-- WHERE filters rows first, then GROUP BY, then HAVING filters groups
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
WHERE hire_date >= '2019-01-01'   -- filter rows first
GROUP BY dept_id
HAVING AVG(salary) > 75000;       -- then filter groups
```

### DISTINCT

Removes duplicate values from the result.

```sql
-- Unique department IDs
SELECT DISTINCT dept_id FROM employees;

-- Unique combinations of dept and a salary range
SELECT DISTINCT dept_id, salary FROM employees;

-- Count of distinct departments
SELECT COUNT(DISTINCT dept_id) AS dept_count FROM employees;
```

### LIMIT (and OFFSET)

Controls how many rows are returned. Syntax varies slightly by database.

```sql
-- MySQL / PostgreSQL
SELECT * FROM employees ORDER BY salary DESC LIMIT 3;

-- Skip first 2 rows, get next 3 (pagination)
SELECT * FROM employees ORDER BY salary DESC LIMIT 3 OFFSET 2;

-- SQL Server equivalent
SELECT TOP 3 * FROM employees ORDER BY salary DESC;

-- Oracle equivalent
SELECT * FROM employees WHERE ROWNUM <= 3 ORDER BY salary DESC;
```

**Pagination pattern (used in APIs):**
```sql
-- Page 1: rows 1-10
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 0;

-- Page 2: rows 11-20
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 10;

-- Page N:
-- OFFSET = (page_number - 1) * page_size
```

### CASE

Adds conditional logic directly in a SQL query (like if-else).

```sql
-- Simple CASE (like switch-case)
SELECT name, dept_id,
    CASE dept_id
        WHEN 10 THEN 'Engineering'
        WHEN 20 THEN 'Marketing'
        WHEN 30 THEN 'HR'
        ELSE 'Unknown'
    END AS department_name
FROM employees;

-- Searched CASE (like if-else if)
SELECT name, salary,
    CASE
        WHEN salary >= 90000 THEN 'Senior'
        WHEN salary >= 75000 THEN 'Mid-Level'
        ELSE 'Junior'
    END AS seniority_level
FROM employees;

-- CASE in ORDER BY
SELECT name, salary
FROM employees
ORDER BY
    CASE WHEN dept_id = 10 THEN 0 ELSE 1 END, -- Engineering first
    salary DESC;

-- CASE in aggregate
SELECT
    SUM(CASE WHEN dept_id = 10 THEN 1 ELSE 0 END) AS engineering_count,
    SUM(CASE WHEN dept_id = 20 THEN 1 ELSE 0 END) AS marketing_count
FROM employees;
```

### SQL Query Execution Order

This is one of the most important concepts for interviews — SQL does NOT execute in the order you write it:

```
Logical Execution Order:
1. FROM          -- identify the source tables
2. JOIN          -- combine tables
3. WHERE         -- filter rows
4. GROUP BY      -- group remaining rows
5. HAVING        -- filter groups
6. SELECT        -- choose columns and expressions
7. DISTINCT      -- remove duplicates
8. ORDER BY      -- sort results
9. LIMIT/OFFSET  -- restrict row count
```

**Why this matters:**
- You cannot use a SELECT alias in a WHERE clause (WHERE runs before SELECT)
- You CAN use a SELECT alias in ORDER BY (ORDER BY runs after SELECT)
- HAVING can reference aggregate functions; WHERE cannot

### Interview Questions - SQL Queries

**Q: What is the difference between WHERE and HAVING?**
> WHERE filters individual rows before grouping. HAVING filters groups after GROUP BY. You can use aggregate functions in HAVING but not in WHERE.

**Q: In what order does SQL execute a query?**
> FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT

**Q: Can you use a column alias in the WHERE clause?**
> No. WHERE is evaluated before SELECT, so the alias doesn't exist yet. You must use the full expression or use a subquery/CTE.

---

> **Key Takeaways - Section 4**
> - SQL execution order: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT.
> - WHERE filters rows; HAVING filters groups.
> - CASE is SQL's conditional logic — works in SELECT, WHERE, ORDER BY, and aggregate functions.
> - LIMIT + OFFSET enables pagination.

---

## 5. SQL Joins

A **JOIN** combines rows from two or more tables based on a related column.

### Sample Tables for Joins

```
employees:                          departments:
+----+-------+---------+            +---------+-------------+
| id | name  | dept_id |            | dept_id | dept_name   |
+----+-------+---------+            +---------+-------------+
|  1 | Alice |    10   |            |    10   | Engineering |
|  2 | Bob   |    20   |            |    20   | Marketing   |
|  3 | Carol |    30   |            |    40   | Finance     |
|  4 | David |   NULL  |            +---------+-------------+
+----+-------+---------+
```

*Note: Carol's dept_id=30 has no matching department. David has NULL dept_id. Finance (40) has no employees.*

---

### INNER JOIN

Returns only the rows that have **matching values in both tables**.

```
Visual:
  employees     departments
  [  [MATCH]  ]
      ↑
  Only matching rows
```

```sql
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;

-- Result:
-- Alice    | Engineering
-- Bob      | Marketing
-- (Carol and David excluded - no match)
-- (Finance excluded - no employees)
```

**Common interview question:** "What happens to rows with no match?" → They are excluded entirely.

---

### LEFT JOIN (LEFT OUTER JOIN)

Returns **all rows from the left table**, and matching rows from the right table. If no match, right side is NULL.

```
Visual:
  [ALL LEFT] [MATCH]
  ↑ Everything from left table
```

```sql
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- Result:
-- Alice    | Engineering
-- Bob      | Marketing
-- Carol    | NULL          (dept_id 30 not in departments)
-- David    | NULL          (NULL dept_id)
```

**Use case:** "Give me all employees, and show their department if they have one."

---

### RIGHT JOIN (RIGHT OUTER JOIN)

Returns **all rows from the right table**, and matching rows from the left table. If no match, left side is NULL.

```
Visual:
  [MATCH] [ALL RIGHT]
              ↑ Everything from right table
```

```sql
SELECT e.name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;

-- Result:
-- Alice    | Engineering
-- Bob      | Marketing
-- NULL     | Finance       (Finance has no employees)
-- (Carol and David excluded)
```

**Note:** RIGHT JOIN can always be rewritten as a LEFT JOIN by swapping table order. Most developers prefer LEFT JOIN for readability.

---

### FULL OUTER JOIN

Returns **all rows from both tables**. Unmatched rows on either side show NULL.

```
Visual:
  [ALL LEFT] [MATCH] [ALL RIGHT]
  ↑ Everything from both tables
```

```sql
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;

-- Result:
-- Alice    | Engineering
-- Bob      | Marketing
-- Carol    | NULL          (no matching dept)
-- David    | NULL          (NULL dept_id)
-- NULL     | Finance       (no employees)
```

**MySQL does not support FULL OUTER JOIN directly.** Workaround:

```sql
SELECT e.name, d.dept_name FROM employees e LEFT JOIN departments d ON e.dept_id = d.dept_id
UNION
SELECT e.name, d.dept_name FROM employees e RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

---

### SELF JOIN

A table **joins with itself**. Useful for hierarchical data (employees and their managers).

```
employees:
+----+----------+-----------+
| id | name     | manager_id|
+----+----------+-----------+
|  1 | Alice    |   NULL    |  (CEO)
|  2 | Bob      |    1      |
|  3 | Carol    |    1      |
|  4 | David    |    2      |
+----+----------+-----------+
```

```sql
-- Find each employee and their manager's name
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

-- Result:
-- Bob   | Alice
-- Carol | Alice
-- David | Bob
-- Alice | NULL   (no manager)
```

---

### CROSS JOIN

Returns the **Cartesian product** — every row from the first table paired with every row from the second table.

```sql
SELECT e.name, d.dept_name
FROM employees e
CROSS JOIN departments d;

-- If employees has 4 rows and departments has 3 rows:
-- Result has 4 × 3 = 12 rows
```

**Use case:** Generate all combinations (e.g., all possible shirt sizes × colors).

```sql
SELECT size, color
FROM shirt_sizes
CROSS JOIN shirt_colors;
```

---

### Join Comparison Table

| Join Type | Returns | Left Nulls | Right Nulls |
|---|---|---|---|
| INNER JOIN | Matching rows only | No | No |
| LEFT JOIN | All left + matching right | No | Yes |
| RIGHT JOIN | All right + matching left | Yes | No |
| FULL OUTER JOIN | All rows from both | Yes | Yes |
| SELF JOIN | Rows joined to themselves | Depends | Depends |
| CROSS JOIN | Every combination | No | No |

### Interview Questions - Joins

**Q: What is the difference between INNER JOIN and LEFT JOIN?**
> INNER JOIN returns only rows with matches in both tables. LEFT JOIN returns all rows from the left table, filling the right side with NULLs where there's no match.

**Q: When would you use a SELF JOIN?**
> When data has hierarchical relationships within the same table — like employees and their managers, or categories with parent categories.

**Q: Can you join on multiple columns?**
> Yes: `JOIN table2 ON t1.col1 = t2.col1 AND t1.col2 = t2.col2`

**Q: What is the difference between JOIN ON and JOIN USING?**
> `JOIN ON` lets you specify any condition. `JOIN USING (column)` is a shorthand when both tables share the same column name.

```sql
-- These are equivalent:
SELECT * FROM employees JOIN departments USING (dept_id);
SELECT * FROM employees JOIN departments ON employees.dept_id = departments.dept_id;
```

**Q: Find employees who don't belong to any department.**
```sql
SELECT e.name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_id IS NULL;
```

---

> **Key Takeaways - Section 5**
> - INNER JOIN = intersection (matching rows only).
> - LEFT JOIN = all left rows + matched right rows.
> - FULL OUTER JOIN = union (all rows from both tables).
> - SELF JOIN = a table joined to itself (hierarchical data).
> - CROSS JOIN = Cartesian product (all combinations). Use carefully — can produce huge result sets.

---

## 6. Advanced SQL

### Subqueries

A **subquery** (inner query) is a SELECT statement nested inside another query. It runs first and passes its result to the outer query.

#### Subquery in WHERE

```sql
-- Find employees earning more than the company average
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Find employees in the 'Engineering' department
SELECT name FROM employees
WHERE dept_id = (SELECT dept_id FROM departments WHERE dept_name = 'Engineering');
```

#### Subquery in FROM (Derived Table)

```sql
-- Find departments where the average salary exceeds 80000
SELECT dept_id, avg_salary
FROM (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
) AS dept_averages
WHERE avg_salary > 80000;
```

#### Subquery in SELECT

```sql
-- Show each employee's salary vs department average
SELECT
    name,
    salary,
    (SELECT AVG(salary) FROM employees e2 WHERE e2.dept_id = e1.dept_id) AS dept_avg
FROM employees e1;
```

#### IN, ANY, ALL with Subqueries

```sql
-- IN: employee in any of these departments
SELECT name FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'NYC');

-- ANY: salary greater than any junior employee
SELECT name FROM employees
WHERE salary > ANY (SELECT salary FROM employees WHERE level = 'Junior');

-- ALL: salary greater than ALL junior employees
SELECT name FROM employees
WHERE salary > ALL (SELECT salary FROM employees WHERE level = 'Junior');

-- EXISTS: check if matching rows exist
SELECT name FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id
);
```

### Correlated Subqueries

A **correlated subquery** references a column from the outer query. It runs once **for each row** of the outer query (unlike a regular subquery which runs once).

```sql
-- Find employees earning more than their department's average
SELECT name, salary, dept_id
FROM employees e1
WHERE salary > (
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.dept_id = e1.dept_id  -- references outer query's dept_id
);
```

**Performance warning:** Correlated subqueries can be slow because they execute once per row. Often replaceable with a JOIN or window function.

```sql
-- More efficient equivalent using JOIN:
SELECT e.name, e.salary, e.dept_id
FROM employees e
JOIN (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
) dept_avg ON e.dept_id = dept_avg.dept_id
WHERE e.salary > dept_avg.avg_salary;
```

### Common Table Expressions (CTEs)

A **CTE** is a named temporary result set defined at the top of a query with the `WITH` keyword. It makes complex queries readable and reusable within the same query.

```sql
-- Basic CTE syntax
WITH cte_name AS (
    SELECT ...
)
SELECT * FROM cte_name;
```

```sql
-- Example: Find departments with above-average employee count
WITH dept_counts AS (
    SELECT dept_id, COUNT(*) AS emp_count
    FROM employees
    GROUP BY dept_id
),
avg_count AS (
    SELECT AVG(emp_count) AS avg_emp_count
    FROM dept_counts
)
SELECT dept_id, emp_count
FROM dept_counts
WHERE emp_count > (SELECT avg_emp_count FROM avg_count);
```

**CTE vs Subquery:**
| Feature | Subquery | CTE |
|---|---|---|
| Readability | Harder for complex queries | Much easier |
| Reusability | Must repeat the query | Reference by name multiple times |
| Recursion | Not possible | Possible with recursive CTE |
| Performance | Similar | Similar (both are optimized the same way in most databases) |

### Window Functions

**Window functions** perform calculations across a set of rows **related to the current row**, without collapsing rows like GROUP BY does.

```sql
-- Syntax
function_name() OVER (
    PARTITION BY column   -- optional: divide rows into groups
    ORDER BY column       -- optional: order within the partition
    ROWS/RANGE BETWEEN... -- optional: frame specification
)
```

```sql
-- Running total of salary
SELECT name, salary,
    SUM(salary) OVER (ORDER BY id) AS running_total
FROM employees;

-- Running total per department
SELECT name, dept_id, salary,
    SUM(salary) OVER (PARTITION BY dept_id ORDER BY id) AS dept_running_total
FROM employees;

-- Average salary alongside each row (no GROUP BY collapse)
SELECT name, salary,
    AVG(salary) OVER () AS company_avg,
    AVG(salary) OVER (PARTITION BY dept_id) AS dept_avg
FROM employees;
```

### Ranking Functions

Ranking functions assign rank numbers to rows within a window.

```sql
SELECT name, salary, dept_id,
    ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS row_num,
    RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk,
    DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dense_rnk,
    NTILE(4)     OVER (ORDER BY salary DESC) AS quartile
FROM employees;
```

**Ranking Function Differences (with example: salaries 100, 90, 90, 80):**

| Function | 100 | 90 | 90 | 80 | Description |
|---|---|---|---|---|---|
| `ROW_NUMBER()` | 1 | 2 | 3 | 4 | Always unique, no ties |
| `RANK()` | 1 | 2 | 2 | 4 | Ties get same rank, skips next |
| `DENSE_RANK()` | 1 | 2 | 2 | 3 | Ties get same rank, no skip |
| `NTILE(4)` | 1 | 2 | 3 | 4 | Divides into N equal buckets |

**Common interview pattern: "Find the top N per group"**

```sql
-- Get the top 2 earners per department
WITH ranked AS (
    SELECT name, salary, dept_id,
           ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
    FROM employees
)
SELECT name, salary, dept_id
FROM ranked
WHERE rn <= 2;
```

### LAG and LEAD Functions

Access values from **previous or next rows** in the window.

```sql
SELECT
    name,
    salary,
    LAG(salary, 1)  OVER (ORDER BY id) AS prev_salary,
    LEAD(salary, 1) OVER (ORDER BY id) AS next_salary,
    salary - LAG(salary, 1) OVER (ORDER BY id) AS salary_diff
FROM employees;
```

**Use case:** Calculate month-over-month sales growth.

```sql
SELECT month, revenue,
    LAG(revenue) OVER (ORDER BY month) AS prev_month_revenue,
    revenue - LAG(revenue) OVER (ORDER BY month) AS growth
FROM monthly_sales;
```

### Recursive CTEs

A **recursive CTE** references itself to traverse hierarchical data (org charts, category trees, bill of materials).

```sql
-- Syntax
WITH RECURSIVE cte_name AS (
    -- Anchor: base case (non-recursive part)
    SELECT ...

    UNION ALL

    -- Recursive part: references the CTE itself
    SELECT ... FROM cte_name JOIN ...
)
SELECT * FROM cte_name;
```

```sql
-- Example: Traverse employee hierarchy
WITH RECURSIVE org_chart AS (
    -- Anchor: start with top-level employees (no manager)
    SELECT id, name, manager_id, 0 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: find direct reports of current level
    SELECT e.id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.id
)
SELECT level, name FROM org_chart ORDER BY level, name;
```

### Interview Questions - Advanced SQL

**Q: What is the difference between a subquery and a CTE?**
> Both create temporary result sets. CTEs are defined before the main query using WITH, are named, can be referenced multiple times in the same query, and support recursion. Subqueries are inline and can be harder to read for complex logic.

**Q: What is the difference between RANK() and DENSE_RANK()?**
> Both assign the same rank to tied rows. RANK() skips the next rank(s) after a tie (1,2,2,4). DENSE_RANK() does not skip ranks (1,2,2,3).

**Q: When would you use a window function instead of GROUP BY?**
> When you need to keep individual rows visible while also seeing aggregate values. GROUP BY collapses rows into one per group. Window functions add the aggregate as an additional column without collapsing.

**Q: What is a correlated subquery and why can it be slow?**
> A correlated subquery references columns from the outer query and re-executes for every row processed by the outer query. This makes it O(n) executions of the subquery, which is slow for large tables.

---

> **Key Takeaways - Section 6**
> - Subqueries run inside another query; correlated subqueries run once per outer row (potentially slow).
> - CTEs improve readability and enable recursion — prefer them over complex nested subqueries.
> - Window functions add aggregate context without collapsing rows — essential for ranking, running totals, and period-over-period analysis.
> - ROW_NUMBER() always gives unique ranks; RANK() skips; DENSE_RANK() doesn't skip.

---

## 7. Normalization

**Normalization** is the process of organizing a database to reduce data redundancy and improve data integrity by dividing large tables into smaller, related tables.

### Why Normalize?

**Anomalies in un-normalized data:**
- **Insert anomaly** - Cannot insert data without other unrelated data
- **Update anomaly** - Updating one record requires updating many records
- **Delete anomaly** - Deleting a record accidentally removes other data

### Starting Example: Un-normalized Table

```
StudentCourses (un-normalized):
+------------+----------+----------+-------+-------------+--------+
| student_id | name     | email    |course | instructor  | grade  |
+------------+----------+----------+-------+-------------+--------+
|     1      | Alice    | a@x.com  | Math  | Prof. Smith | A      |
|     1      | Alice    | a@x.com  | CS101 | Prof. Jones | B      |
|     2      | Bob      | b@x.com  | Math  | Prof. Smith | C      |
+------------+----------+----------+-------+-------------+--------+
```

**Problems:**
- Alice's email stored twice → if email changes, must update 2+ rows
- Cannot store a course without a student enrolled
- If Alice drops Math, we lose that Math is taught by Prof. Smith

---

### First Normal Form (1NF)

**Rule:** Each column must contain **atomic (indivisible) values**, and each row must be unique.

**Violations of 1NF:**
- A column contains multiple values: `courses = "Math, CS101, Physics"`
- Repeating column groups: `course1`, `course2`, `course3`

```
Violates 1NF:
+----+-------+-------------------+
| id | name  | courses           |
+----+-------+-------------------+
|  1 | Alice | Math, CS101       |  ← Multi-valued
+----+-------+-------------------+

Fixed (1NF):
+----+-------+-------+
| id | name  | course|
+----+-------+-------+
|  1 | Alice | Math  |
|  1 | Alice | CS101 |
+----+-------+-------+
```

**1NF checklist:**
- No multi-valued columns
- No repeating column groups
- Each row is unique (has a primary key)

---

### Second Normal Form (2NF)

**Rule:** Must be in 1NF, and every **non-key column must depend on the entire composite primary key** (no partial dependencies).

*Only relevant when the primary key is composite.*

```
Violates 2NF (PK = student_id + course):
+------------+-------+----------+-------------+-------+
| student_id | course| email    | instructor  | grade |
+------------+-------+----------+-------------+-------+
|     1      | Math  | a@x.com  | Prof. Smith | A     |

email depends only on student_id (partial dependency)
instructor depends only on course (partial dependency)
grade depends on (student_id, course) - fine
```

**Fix:** Separate into three tables:

```sql
-- Students table (student_id → email)
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name       VARCHAR(100),
    email      VARCHAR(100)
);

-- Courses table (course → instructor)
CREATE TABLE courses (
    course     VARCHAR(50) PRIMARY KEY,
    instructor VARCHAR(100)
);

-- Enrollments table (student_id + course → grade)
CREATE TABLE enrollments (
    student_id INT,
    course     VARCHAR(50),
    grade      CHAR(2),
    PRIMARY KEY (student_id, course),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course) REFERENCES courses(course)
);
```

---

### Third Normal Form (3NF)

**Rule:** Must be in 2NF, and every **non-key column must depend directly on the primary key**, not on another non-key column (no transitive dependencies).

```
Violates 3NF:
+----------+------+-----------+-----------+
| order_id | item | zip_code  | city      |
+----------+------+-----------+-----------+
|    1     | Pen  | 10001     | New York  |

zip_code → city (transitive dependency: city depends on zip, not order_id)
```

**Fix:**

```sql
-- Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    item     VARCHAR(100),
    zip_code VARCHAR(10),
    FOREIGN KEY (zip_code) REFERENCES zip_codes(zip_code)
);

-- ZipCodes table (zip_code → city)
CREATE TABLE zip_codes (
    zip_code VARCHAR(10) PRIMARY KEY,
    city     VARCHAR(100)
);
```

---

### Boyce-Codd Normal Form (BCNF)

**Rule:** A stricter version of 3NF. For every functional dependency X → Y, X must be a **superkey**.

BCNF handles rare edge cases where 3NF tables still have anomalies due to overlapping candidate keys.

```
Violates BCNF:
+----------+--------+-------+
| student  | course | tutor |
+----------+--------+-------+
Each tutor teaches only one course, one student can have one tutor per course.
FDs: (student, course) → tutor, tutor → course
tutor → course violates BCNF because 'tutor' is not a superkey
```

**Fix:** Split into two tables:
- `(student, tutor)` - which tutor a student has
- `(tutor, course)` - which course each tutor teaches

---

### Normalization Summary

| Normal Form | Requirement | Eliminates |
|---|---|---|
| 1NF | Atomic values, unique rows | Multi-valued columns, repeating groups |
| 2NF | 1NF + no partial dependencies | Partial dependencies on composite keys |
| 3NF | 2NF + no transitive dependencies | Non-key columns depending on other non-key columns |
| BCNF | 3NF + every determinant is a superkey | Certain anomalies in overlapping candidate keys |

---

### Denormalization

**Denormalization** deliberately introduces redundancy to improve **read performance**.

**When to denormalize:**
- Read-heavy workloads (reporting, analytics)
- Joins are too slow and costly
- Data is updated infrequently

```sql
-- Normalized: requires JOIN every query
SELECT o.order_id, c.name, c.email
FROM orders o JOIN customers c ON o.customer_id = c.id;

-- Denormalized: customer name stored in orders table
SELECT order_id, customer_name, customer_email FROM orders;
-- Faster read, but if customer updates email, must update all order rows too
```

**Trade-off table:**

| Aspect | Normalized | Denormalized |
|---|---|---|
| Storage | Less (less redundancy) | More (duplicate data) |
| Read performance | Slower (more joins) | Faster (fewer joins) |
| Write performance | Faster (update once) | Slower (update many) |
| Data consistency | Easier to maintain | Risk of inconsistency |
| Best for | OLTP (transactional) | OLAP (analytical) |

### Interview Questions - Normalization

**Q: What is a transitive dependency?**
> When a non-key column depends on another non-key column instead of the primary key directly. Example: `zip_code → city` where neither is the primary key.

**Q: Why would you denormalize a database?**
> To improve read performance for reporting and analytics. When queries involve many expensive joins, denormalization reduces them at the cost of data redundancy and more complex updates.

**Q: Is a database always supposed to be in BCNF?**
> Not necessarily. For practical OLTP systems, 3NF is usually sufficient. BCNF can sometimes eliminate the ability to represent certain functional dependencies without joins. OLAP/data warehouse systems are often denormalized intentionally.

---

> **Key Takeaways - Section 7**
> - Normalization reduces redundancy and prevents insert/update/delete anomalies.
> - 1NF = atomic values; 2NF = no partial dependencies; 3NF = no transitive dependencies.
> - Denormalization improves read performance but sacrifices consistency — used in analytics/reporting.
> - In practice, target 3NF for OLTP systems.

---

## 8. Indexing

### What is an Index?

An **index** is a data structure (typically a B-Tree) that the database maintains alongside a table to allow fast data lookup — like the index in the back of a book.

**Without index:** Database scans every row (full table scan) — O(n)
**With index:** Database jumps directly to the relevant rows — O(log n)

```sql
-- Create a basic index
CREATE INDEX idx_employees_salary ON employees(salary);

-- Create a unique index
CREATE UNIQUE INDEX idx_employees_email ON employees(email);

-- Drop an index
DROP INDEX idx_employees_salary ON employees;

-- Check if a query uses an index (MySQL)
EXPLAIN SELECT * FROM employees WHERE salary > 80000;
```

### Clustered vs Non-Clustered Index

#### Clustered Index

- **Definition:** The table data is physically sorted and stored according to the index key.
- There can be **only one clustered index** per table (because data can only be physically sorted one way).
- In most databases, the **primary key automatically becomes the clustered index**.

```
Clustered index on employee_id:
Physical storage:
[id=1: Alice, 80000] [id=2: Bob, 90000] [id=3: Carol, 75000]
                ↑ Data rows ARE the index leaf nodes
```

#### Non-Clustered Index

- **Definition:** A separate structure that stores index keys + **pointers** to the actual data rows.
- A table can have **many non-clustered indexes**.
- Slightly slower than clustered index (requires an extra lookup to get the actual row).

```
Non-clustered index on salary:
Index structure:     Pointer to actual row:
[75000] ──────────► [id=3: Carol, 75000]
[80000] ──────────► [id=1: Alice, 80000]
[90000] ──────────► [id=2: Bob,   90000]
```

**Comparison:**

| Feature | Clustered Index | Non-Clustered Index |
|---|---|---|
| Data storage | Data IS the index | Separate structure with pointers |
| Count per table | Only 1 | Many (usually up to 999 in SQL Server) |
| Speed | Faster | Slightly slower (extra pointer lookup) |
| Default | Primary key | Any indexed column |

### Composite Index

An index on **two or more columns**. The order of columns matters significantly.

```sql
-- Composite index on (last_name, first_name)
CREATE INDEX idx_name ON employees(last_name, first_name);
```

**The leftmost prefix rule:** A composite index on (A, B, C) can efficiently support queries on:
- A
- A, B
- A, B, C

But NOT:
- B alone
- C alone
- B, C

```sql
-- Uses index: ✓
SELECT * FROM employees WHERE last_name = 'Smith';
SELECT * FROM employees WHERE last_name = 'Smith' AND first_name = 'Alice';

-- Does NOT use index efficiently: ✗
SELECT * FROM employees WHERE first_name = 'Alice';  -- skips last_name
```

### Covering Index

A **covering index** includes all columns needed by a query, so the database never needs to look up the actual table row.

```sql
-- Query needs: name, salary, dept_id
SELECT name, salary, dept_id FROM employees WHERE dept_id = 10;

-- Covering index: includes all three columns
CREATE INDEX idx_covering ON employees(dept_id, name, salary);
-- Index itself has all needed columns → no table lookup needed (index-only scan)
```

### When Indexes Hurt Performance

Indexes are not free — they have costs:

- **Write overhead:** Every INSERT, UPDATE, DELETE must also update the index.
- **Storage cost:** Each index uses additional disk space.
- **Too many indexes** slow down writes more than they speed up reads.

**Avoid indexing:**
- Columns with very low cardinality (few unique values), e.g., a `gender` column with only M/F
- Small tables (a full scan may be faster)
- Columns rarely used in WHERE, JOIN, or ORDER BY

**Good candidates for indexing:**
- Primary key (auto-indexed)
- Foreign key columns (used in JOINs)
- Columns frequently used in WHERE clauses
- Columns used in ORDER BY or GROUP BY

### Interview Questions - Indexing

**Q: Why can a table have only one clustered index?**
> The clustered index determines the physical order in which data is stored on disk. Data can only be physically sorted one way, so only one clustered index is possible.

**Q: What is an index scan vs index seek?**
> An index seek jumps directly to the relevant part of the index (efficient). An index scan reads the entire index from start to finish (less efficient, but sometimes unavoidable for range queries or when selectivity is low).

**Q: You added indexes but the query is still slow. What would you check?**
> 1. Check if the query is actually using the index with EXPLAIN. 2. Check if the index matches the query's column order (leftmost prefix rule). 3. Check if the query's WHERE clause uses functions on indexed columns (disables index). 4. Check table statistics — outdated stats can cause bad query plans.

**Common mistake:** `WHERE YEAR(hire_date) = 2020` — applying a function to an indexed column prevents index usage. Instead: `WHERE hire_date BETWEEN '2020-01-01' AND '2020-12-31'`.

---

> **Key Takeaways - Section 8**
> - Index = fast lookup at the cost of slower writes and extra storage.
> - Clustered index = data stored in index order (only 1 per table, usually the PK).
> - Non-clustered index = separate structure with pointers to rows (many per table).
> - Composite index: column order matters — leftmost prefix rule applies.
> - Avoid indexing low-cardinality columns or applying functions to indexed columns in WHERE.

---

## 9. Transactions

### What is a Transaction?

A **transaction** is a sequence of one or more SQL operations treated as a single logical unit of work. Either all operations succeed, or none of them take effect.

**Classic example:** Bank transfer — debit one account, credit another. Both must succeed or both must fail.

```sql
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;  -- Debit
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;  -- Credit

-- Both succeeded? Save it.
COMMIT;

-- Something went wrong? Undo everything.
ROLLBACK;
```

### ACID Properties

ACID is the cornerstone of reliable database transactions.

#### A - Atomicity

"All or nothing." A transaction is treated as a single unit — either all operations complete successfully, or none of them do.

**Example:** In a bank transfer, if the debit succeeds but the credit fails, atomicity ensures the debit is rolled back too.

#### C - Consistency

A transaction brings the database from one **valid state** to another valid state. All rules (constraints, cascades, triggers) must hold before and after the transaction.

**Example:** A bank account balance cannot go negative if there's a check constraint. A transaction violating this is rejected, keeping the database consistent.

#### I - Isolation

Concurrent transactions execute as if they were running **serially** (one after another). The intermediate state of a transaction is not visible to other transactions.

**Example:** Two transactions both reading and updating the same account balance should not interfere with each other.

#### D - Durability

Once a transaction is committed, its changes are **permanent** — even in the event of a system crash or power failure. Changes are written to persistent storage (disk/write-ahead log).

**ACID Summary:**

| Property | Guarantee | Managed By |
|---|---|---|
| Atomicity | All or nothing | Rollback logs (undo logs) |
| Consistency | Valid state transitions | Constraints, triggers |
| Isolation | No interference between transactions | Locks, MVCC |
| Durability | Committed data survives crashes | Write-ahead logs (WAL) |

### Transaction Lifecycle

```
BEGIN TRANSACTION
        │
        ▼
   Execute SQL statements
        │
    ┌───┴────┐
    │        │
  COMMIT   ROLLBACK
    │        │
    ▼        ▼
Changes   Changes
 saved    undone
```

### COMMIT

Permanently saves all changes made in the current transaction.

```sql
BEGIN TRANSACTION;
INSERT INTO orders (customer_id, amount) VALUES (1, 250.00);
INSERT INTO order_items (order_id, product_id, qty) VALUES (LAST_INSERT_ID(), 3, 2);
COMMIT;  -- Both inserts are now permanent
```

### ROLLBACK

Undoes all changes made in the current transaction.

```sql
BEGIN TRANSACTION;
UPDATE inventory SET quantity = quantity - 5 WHERE product_id = 3;

-- Oops, product doesn't exist or quantity went negative
ROLLBACK;  -- quantity update is undone
```

### SAVEPOINT

Creates a **checkpoint** within a transaction so you can partially roll back without abandoning the entire transaction.

```sql
BEGIN TRANSACTION;

INSERT INTO orders (customer_id, amount) VALUES (1, 500);  -- Step 1
SAVEPOINT after_order;

INSERT INTO payments (order_id, amount) VALUES (LAST_INSERT_ID(), 500);  -- Step 2
SAVEPOINT after_payment;

INSERT INTO invoices (order_id) VALUES (LAST_INSERT_ID());  -- Step 3

-- Invoice step failed, but we want to keep the order and payment
ROLLBACK TO SAVEPOINT after_payment;  -- Only step 3 is undone

COMMIT;  -- Steps 1 and 2 are committed
```

### Autocommit

Most databases operate in **autocommit mode** by default, meaning each individual SQL statement is automatically committed.

```sql
-- Disable autocommit (MySQL)
SET autocommit = 0;

-- Or explicitly start a transaction
START TRANSACTION;  -- implicitly disables autocommit for this transaction
```

### Interview Questions - Transactions

**Q: What does ACID stand for and why does it matter?**
> Atomicity (all or nothing), Consistency (valid state), Isolation (no interference), Durability (committed = permanent). ACID ensures that database transactions are reliable even in the event of failures or concurrent access.

**Q: Can you explain a scenario where atomicity is critical?**
> A bank transfer: debit $500 from Account A and credit $500 to Account B. If the debit succeeds but the system crashes before the credit, the money disappears. Atomicity guarantees that if any part fails, the entire transaction is rolled back.

**Q: What is the difference between ROLLBACK and ROLLBACK TO SAVEPOINT?**
> `ROLLBACK` undoes the entire transaction. `ROLLBACK TO SAVEPOINT` only undoes the changes made after that savepoint, preserving earlier changes within the same transaction.

---

> **Key Takeaways - Section 9**
> - A transaction is a unit of work: all succeeds or all fails.
> - ACID = Atomicity + Consistency + Isolation + Durability.
> - COMMIT saves permanently; ROLLBACK undoes everything.
> - SAVEPOINT allows partial rollback within a transaction.
> - Most databases default to autocommit — explicit transactions require `BEGIN TRANSACTION`.

---

## 10. Concurrency Control

When multiple transactions run simultaneously, problems can occur if isolation is not properly managed.

### Concurrency Problems

#### Dirty Read

A transaction reads data **written by another uncommitted transaction**. If the other transaction rolls back, the read data was invalid ("dirty").

```
T1: UPDATE accounts SET balance = 1000 WHERE id = 1  (not committed yet)
T2: SELECT balance FROM accounts WHERE id = 1  → reads 1000  (DIRTY READ)
T1: ROLLBACK  → balance is actually still 500
T2 acted on invalid data!
```

#### Non-Repeatable Read

A transaction reads the same row **twice** and gets **different values** because another transaction modified and committed it between the two reads.

```
T1: SELECT salary FROM employees WHERE id = 1  → 80000
T2: UPDATE employees SET salary = 90000 WHERE id = 1; COMMIT;
T1: SELECT salary FROM employees WHERE id = 1  → 90000  (DIFFERENT!)
```

#### Phantom Read

A transaction re-executes a query and finds **different rows** (phantom rows appear or disappear) because another transaction inserted or deleted rows matching the query.

```
T1: SELECT * FROM orders WHERE amount > 1000  → returns 5 rows
T2: INSERT INTO orders (amount) VALUES (1500); COMMIT;
T1: SELECT * FROM orders WHERE amount > 1000  → returns 6 rows  (PHANTOM!)
```

#### Lost Update

Two transactions read the same value and both update it. The second update **overwrites** the first update.

```
T1: reads balance = 100
T2: reads balance = 100
T1: writes balance = 150  (added 50)
T2: writes balance = 80   (subtracted 20) — T1's update is LOST
```

### Isolation Levels

Isolation levels define how much one transaction is shielded from others. Higher isolation = fewer anomalies, but more locking and reduced concurrency.

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read | Performance |
|---|---|---|---|---|
| READ UNCOMMITTED | Possible | Possible | Possible | Highest |
| READ COMMITTED | Prevented | Possible | Possible | High |
| REPEATABLE READ | Prevented | Prevented | Possible | Medium |
| SERIALIZABLE | Prevented | Prevented | Prevented | Lowest |

```sql
-- Set isolation level for the current session
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

#### READ UNCOMMITTED
- Transactions can read uncommitted changes from others.
- Allows dirty reads — almost never used in practice.

#### READ COMMITTED (default in PostgreSQL, Oracle, SQL Server)
- A transaction only reads committed data.
- Prevents dirty reads; still allows non-repeatable reads and phantoms.

#### REPEATABLE READ (default in MySQL/InnoDB)
- Data read during a transaction cannot be changed by others until the transaction ends.
- Prevents dirty reads and non-repeatable reads; still allows phantom reads (in most databases).

#### SERIALIZABLE
- Transactions execute as if they were serial (one at a time).
- Prevents all three anomalies but has the highest locking overhead.
- Used for financial systems where correctness is critical.

### MVCC - Multi-Version Concurrency Control

Many modern databases (PostgreSQL, MySQL InnoDB) use **MVCC** to achieve isolation without locking reads.

- Each row has version metadata (transaction ID, timestamps).
- A transaction sees a **snapshot** of the data as it was when the transaction started.
- Readers don't block writers; writers don't block readers.
- Old versions are eventually cleaned up (vacuum in PostgreSQL).

### Interview Questions - Concurrency Control

**Q: What is the difference between a dirty read and a non-repeatable read?**
> A dirty read occurs when you read **uncommitted** data. A non-repeatable read occurs when you read **committed** data twice and get different results because another transaction committed a change in between.

**Q: What isolation level prevents phantom reads?**
> SERIALIZABLE prevents phantom reads. In practice, many databases use predicate locks or next-key locks at the REPEATABLE READ level to also prevent phantoms (MySQL InnoDB does this).

**Q: What is MVCC and why is it useful?**
> Multi-Version Concurrency Control maintains multiple versions of data rows so readers never block writers and writers never block readers. This dramatically improves throughput in read-heavy workloads.

---

> **Key Takeaways - Section 10**
> - Dirty read = reading uncommitted data; Non-repeatable read = same row, different value on re-read; Phantom read = different rows on re-query.
> - Higher isolation level = fewer anomalies + more locking overhead.
> - READ COMMITTED is the practical default for most applications.
> - MVCC lets reads and writes happen concurrently without blocking each other.

---

## 11. Locks

**Locks** are mechanisms that prevent concurrent transactions from interfering with each other.

### Shared Lock (Read Lock)

- Allows multiple transactions to read the same data simultaneously.
- No transaction can **write** to data that has a shared lock held by another.
- Multiple shared locks can coexist on the same data.

```sql
-- Explicitly acquire a shared lock (PostgreSQL)
SELECT * FROM accounts WHERE id = 1 FOR SHARE;
```

### Exclusive Lock (Write Lock)

- Only **one transaction** can hold an exclusive lock on data.
- No other transaction can read or write while an exclusive lock is held.
- Acquired automatically by INSERT, UPDATE, DELETE.

```sql
-- Explicitly acquire an exclusive lock
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
-- Now no other transaction can read or write this row until COMMIT/ROLLBACK
```

### Lock Compatibility Matrix

| | Shared Lock | Exclusive Lock |
|---|---|---|
| **Shared Lock** | Compatible (both can hold) | Incompatible |
| **Exclusive Lock** | Incompatible | Incompatible |

### Optimistic Locking

- **Assumption:** Conflicts are rare. Don't lock data during reads.
- Read data without locking. Before updating, **check if data was modified** since you read it.
- If modified → abort and retry. If not modified → proceed with update.
- Implemented using a **version number** or **timestamp** column.

```sql
-- Schema: version column added
CREATE TABLE products (
    id      INT PRIMARY KEY,
    name    VARCHAR(100),
    stock   INT,
    version INT DEFAULT 0
);

-- Read the row (no lock)
SELECT id, stock, version FROM products WHERE id = 5;
-- Returns: id=5, stock=100, version=3

-- Update ONLY if version hasn't changed
UPDATE products
SET stock = 95, version = version + 1
WHERE id = 5 AND version = 3;

-- If 0 rows affected → someone else updated it → retry
```

**Best for:** Read-heavy workloads with rare conflicts.

### Pessimistic Locking

- **Assumption:** Conflicts are frequent. Lock data as soon as you read it.
- Hold the lock until the transaction ends.
- Prevents any other transaction from modifying the data.

```sql
-- Lock the row immediately on read (FOR UPDATE)
BEGIN TRANSACTION;
SELECT * FROM seats WHERE seat_id = 15 FOR UPDATE;  -- lock acquired
-- Do some processing...
UPDATE seats SET status = 'booked' WHERE seat_id = 15;
COMMIT;  -- lock released
```

**Best for:** Write-heavy workloads, financial transactions, systems where conflicts are common.

### Deadlock

A **deadlock** occurs when two or more transactions are each waiting for the other to release a lock, creating a circular dependency.

```
T1 holds lock on Table A, waiting for lock on Table B
T2 holds lock on Table B, waiting for lock on Table A
→ Neither can proceed → DEADLOCK
```

**How databases handle deadlocks:**
- Detect the cycle and automatically kill one transaction (the victim).
- The killed transaction is rolled back; the application should retry.

**How to prevent deadlocks:**
- Always acquire locks in the same order across all transactions.
- Keep transactions short.
- Use lower isolation levels when possible.
- Use optimistic locking where conflicts are rare.

### Lock Granularity

| Level | Description | Concurrency | Overhead |
|---|---|---|---|
| Row-level | Lock individual rows | Highest | Highest |
| Page-level | Lock a page of rows | Medium | Medium |
| Table-level | Lock the entire table | Lowest | Lowest |

### Interview Questions - Locks

**Q: What is the difference between optimistic and pessimistic locking?**
> Pessimistic locking prevents conflicts by locking data before reading it. Optimistic locking allows reads without locks and detects conflicts at update time using a version number. Pessimistic is better for high-conflict scenarios; optimistic is better for low-conflict, read-heavy systems.

**Q: How do you detect and resolve a deadlock?**
> The database automatically detects deadlocks by checking for cycles in the wait-for graph. It selects a victim transaction to rollback (usually the one that's done less work). Application code should catch deadlock errors and retry the transaction.

**Q: What does SELECT FOR UPDATE do?**
> It reads a row and immediately places an exclusive lock on it, preventing any other transaction from reading (with FOR UPDATE/FOR SHARE) or writing to that row until the current transaction ends.

---

> **Key Takeaways - Section 11**
> - Shared lock = read lock (multiple allowed); Exclusive lock = write lock (only one allowed).
> - Optimistic locking = check version at update time (low conflict scenarios).
> - Pessimistic locking = lock immediately on read (high conflict scenarios).
> - Deadlocks are resolved by the database killing a victim transaction — design code to retry.

---

## 12. Database Design

### Entity-Relationship (ER) Diagrams

An **ER diagram** visually models the entities (objects) in a system and the relationships between them, before creating actual tables.

**Key components:**
- **Entity** - A real-world object (Customer, Order, Product). Becomes a table.
- **Attribute** - A property of an entity (name, email, price). Becomes a column.
- **Relationship** - An association between entities (Customer PLACES Order).
- **Cardinality** - How many instances of one entity relate to another.

### Cardinality Types

| Relationship | Description | Example |
|---|---|---|
| One-to-One (1:1) | One row in A links to exactly one row in B | Person ↔ Passport |
| One-to-Many (1:N) | One row in A links to many rows in B | Customer → Orders |
| Many-to-Many (M:N) | Many rows in A link to many rows in B | Students ↔ Courses |

#### One-to-One (1:1)

```sql
-- Each user has exactly one profile
CREATE TABLE users (
    user_id  INT PRIMARY KEY,
    username VARCHAR(50)
);

CREATE TABLE user_profiles (
    profile_id INT PRIMARY KEY,
    user_id    INT UNIQUE,  -- UNIQUE enforces 1:1
    bio        TEXT,
    avatar_url VARCHAR(255),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

#### One-to-Many (1:N)

```sql
-- One customer can have many orders
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name        VARCHAR(100)
);

CREATE TABLE orders (
    order_id    INT PRIMARY KEY,
    customer_id INT,  -- Foreign key on the "many" side
    amount      DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

#### Many-to-Many (M:N)

Requires a **junction table** (also called a bridge or associative table).

```sql
-- Students can enroll in many courses; courses have many students
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name       VARCHAR(100)
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    title     VARCHAR(100)
);

-- Junction table
CREATE TABLE enrollments (
    student_id INT,
    course_id  INT,
    grade      CHAR(2),
    enrolled_at DATE,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

### Schema Design Best Practices

1. **Choose meaningful names** - Use `customer_id` not `id` in a customers table when joining others.
2. **Use surrogate keys** - Auto-incremented integer PKs are generally better than natural keys (email can change).
3. **Normalize to at least 3NF** for OLTP systems.
4. **Index foreign keys** - Foreign key columns are frequently used in JOINs.
5. **Use appropriate data types** - Don't store numbers as VARCHAR; use DATE for dates not VARCHAR.
6. **Add constraints** - NOT NULL, UNIQUE, CHECK constraints enforce data quality at the database level.
7. **Use consistent naming conventions** - `snake_case` for columns, plural for table names.
8. **Document relationships** - Comment your schema or maintain ER diagrams.
9. **Avoid generic column names** - `type`, `status`, `data` are too vague; be specific.
10. **Plan for soft deletes** - Add `deleted_at TIMESTAMP NULL` instead of physically deleting rows.

```sql
-- Good schema practices example
CREATE TABLE products (
    product_id   INT AUTO_INCREMENT PRIMARY KEY,
    name         VARCHAR(255) NOT NULL,
    description  TEXT,
    price        DECIMAL(10,2) NOT NULL CHECK (price >= 0),
    stock_qty    INT NOT NULL DEFAULT 0 CHECK (stock_qty >= 0),
    category_id  INT NOT NULL,
    is_active    BOOLEAN NOT NULL DEFAULT TRUE,
    created_at   TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at   TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at   TIMESTAMP NULL,     -- soft delete
    FOREIGN KEY (category_id) REFERENCES categories(category_id),
    INDEX idx_category (category_id),
    INDEX idx_price (price)
);
```

### Interview Questions - Database Design

**Q: How would you handle a many-to-many relationship in a relational database?**
> By creating a junction (bridge) table that contains the primary keys of both related tables as foreign keys. The junction table's primary key is usually a composite key of both foreign keys.

**Q: What is the difference between a natural key and a surrogate key?**
> A natural key is a meaningful real-world attribute (email, SSN, ISBN). A surrogate key is an artificial system-generated identifier (auto-incremented integer, UUID). Surrogate keys are preferred because natural keys can change and may not always be unique.

**Q: When would you use a soft delete instead of a hard delete?**
> When you need audit trails, the ability to restore deleted records, or when other systems may reference the deleted record. Soft deletes add a `deleted_at` timestamp and filter queries with `WHERE deleted_at IS NULL`.

---

> **Key Takeaways - Section 12**
> - ER diagrams model entities and relationships before creating tables.
> - 1:1 use UNIQUE FK; 1:N use FK on the "many" side; M:N use a junction table.
> - Use surrogate keys, proper data types, and constraints for clean schema design.
> - Always index foreign key columns.

---

## 13. Query Optimization

### How Query Execution Works

When you submit a SQL query, the database processes it in several stages:

```
1. Parsing          → Check syntax, build parse tree
2. Semantic Analysis → Check table/column names, permissions
3. Query Rewriting  → Apply logical transformations
4. Query Planning   → Generate possible execution plans
5. Plan Selection   → Query optimizer picks the best plan (cost-based)
6. Execution        → Execute the chosen plan
7. Result Return    → Send results to client
```

The **Query Optimizer** is the most important component — it analyzes statistics about table sizes, index availability, and data distribution to estimate the cost of different execution plans.

### EXPLAIN / EXPLAIN ANALYZE

`EXPLAIN` shows the execution plan the database will use without running the query. `EXPLAIN ANALYZE` actually runs the query and shows real vs estimated costs.

```sql
-- MySQL
EXPLAIN SELECT * FROM employees WHERE salary > 80000;

-- PostgreSQL
EXPLAIN ANALYZE SELECT * FROM employees WHERE salary > 80000;
```

**Key things to look for in an EXPLAIN output:**

| Term | What It Means | Good/Bad |
|---|---|---|
| `seq scan` / `table scan` | Reading every row | Bad for large tables |
| `index scan` | Using an index | Good |
| `index seek` | Jumping directly to index location | Best |
| `nested loop join` | For each row in T1, scan T2 | OK for small tables |
| `hash join` | Build hash table, probe with other table | Good for larger tables |
| `merge join` | Both tables sorted and merged | Good when both sides are sorted |
| `rows` estimate | Estimated number of rows processed | High estimate = potential bottleneck |

### Common Performance Bottlenecks

1. **Missing indexes** on WHERE, JOIN, and ORDER BY columns
2. **Full table scans** on large tables
3. **SELECT \*** retrieving more columns than needed
4. **N+1 query problem** — one query per row of a result set (common in ORM usage)
5. **Functions on indexed columns** in WHERE clause (disables index)
6. **Implicit type conversions** (e.g., comparing INT column to a string)
7. **Unoptimized JOINs** on non-indexed foreign keys
8. **Outdated statistics** causing the optimizer to choose a bad plan
9. **Large OFFSET pagination** (LIMIT 10 OFFSET 1000000 still reads 1 million rows)
10. **Locking contention** from long-running transactions

### Optimization Techniques

#### 1. Use Indexes Appropriately

```sql
-- Slow: function on indexed column disables index
SELECT * FROM employees WHERE YEAR(hire_date) = 2020;

-- Fast: range query uses index
SELECT * FROM employees WHERE hire_date BETWEEN '2020-01-01' AND '2020-12-31';
```

#### 2. Avoid SELECT *

```sql
-- Slow: fetches all columns (unnecessary I/O)
SELECT * FROM orders WHERE customer_id = 1;

-- Fast: fetch only needed columns
SELECT order_id, amount, created_at FROM orders WHERE customer_id = 1;
```

#### 3. Use EXISTS Instead of COUNT for Existence Checks

```sql
-- Slow: counts all matching rows
IF (SELECT COUNT(*) FROM orders WHERE customer_id = 1) > 0

-- Fast: stops at first match
IF EXISTS (SELECT 1 FROM orders WHERE customer_id = 1)
```

#### 4. Avoid OFFSET for Deep Pagination — Use Keyset Pagination

```sql
-- Slow: reads and discards 1 million rows
SELECT * FROM orders ORDER BY order_id LIMIT 10 OFFSET 1000000;

-- Fast: keyset/cursor-based pagination
SELECT * FROM orders
WHERE order_id > 1000000  -- last seen ID
ORDER BY order_id
LIMIT 10;
```

#### 5. Optimize JOINs

```sql
-- Ensure JOIN columns are indexed
CREATE INDEX idx_orders_customer_id ON orders(customer_id);

-- Filter early with WHERE before joining
SELECT o.order_id, c.name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.created_at >= '2024-01-01';  -- filter early
```

#### 6. Use CTEs / Subqueries Wisely

```sql
-- Materializing a CTE can help (PostgreSQL) or hurt (MySQL) performance
-- In PostgreSQL pre-12, CTEs were always materialized (run once, results stored)
-- Know your database's behavior
```

#### 7. Update Statistics

```sql
-- MySQL
ANALYZE TABLE employees;

-- PostgreSQL
ANALYZE employees;

-- SQL Server
UPDATE STATISTICS employees;
```

#### 8. The N+1 Query Problem

```sql
-- N+1 Problem (application code loop):
-- Query 1: SELECT * FROM orders LIMIT 100  → 100 orders
-- Then for each order: SELECT * FROM customers WHERE id = ?  → 100 MORE queries
-- Total: 101 queries

-- Solution: JOIN everything in one query
SELECT o.order_id, o.amount, c.name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
LIMIT 100;
-- Total: 1 query
```

### Interview Questions - Query Optimization

**Q: How would you approach optimizing a slow query?**
> 1. Run EXPLAIN/EXPLAIN ANALYZE to see the execution plan. 2. Check if a full table scan is occurring. 3. Add indexes on filtered/joined columns. 4. Ensure no functions are applied to indexed columns in WHERE. 5. Reduce columns selected (avoid SELECT *). 6. Check for N+1 patterns. 7. Analyze if statistics are up to date. 8. Consider query rewriting using CTEs or subqueries differently.

**Q: What is the N+1 query problem?**
> It occurs when you run one query to get N records and then run N additional queries to fetch related data for each record. It's common with ORMs. The fix is to use JOINs or batch loading to fetch all needed data in one or a few queries.

---

> **Key Takeaways - Section 13**
> - Use EXPLAIN to see the query execution plan — look for table scans on large tables.
> - Never apply functions to indexed columns in WHERE clauses.
> - Avoid SELECT * and deep OFFSET pagination.
> - The N+1 query problem is the most common ORM-related performance issue — fix with JOINs.
> - Keep statistics updated so the optimizer makes good decisions.

---

## 14. Stored Procedures, Views, and Triggers

### Views

A **view** is a virtual table based on a stored SQL query. It does not store data itself (usually) — it runs the underlying query when accessed.

```sql
-- Create a view
CREATE VIEW employee_summary AS
SELECT e.name, e.salary, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- Query the view like a table
SELECT * FROM employee_summary WHERE dept_name = 'Engineering';

-- Update a view
CREATE OR REPLACE VIEW employee_summary AS
SELECT e.name, e.salary, d.dept_name, e.hire_date
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- Drop a view
DROP VIEW employee_summary;
```

**Benefits of Views:**
- Simplify complex queries for end users
- Security — expose only certain columns/rows to specific users
- Centralize business logic — one definition, reused everywhere

**Materialized Views:** Unlike regular views, materialized views store the query result physically and refresh it periodically. Great for expensive reports.

```sql
-- PostgreSQL materialized view
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT DATE_TRUNC('month', order_date) AS month, SUM(amount) AS total
FROM orders
GROUP BY DATE_TRUNC('month', order_date);

-- Refresh manually
REFRESH MATERIALIZED VIEW monthly_sales;
```

### Stored Procedures

A **stored procedure** is a precompiled set of SQL statements stored in the database that can be called by name with parameters.

```sql
-- Create a stored procedure (MySQL syntax)
DELIMITER //
CREATE PROCEDURE GetEmployeesByDept(IN dept INT, IN min_salary DECIMAL(10,2))
BEGIN
    SELECT name, salary
    FROM employees
    WHERE dept_id = dept AND salary >= min_salary
    ORDER BY salary DESC;
END //
DELIMITER ;

-- Call the procedure
CALL GetEmployeesByDept(10, 75000);

-- Procedure with OUT parameter
DELIMITER //
CREATE PROCEDURE GetDeptBudget(IN dept_id INT, OUT budget DECIMAL(10,2))
BEGIN
    SELECT d.budget INTO budget
    FROM departments d
    WHERE d.dept_id = dept_id;
END //
DELIMITER ;

CALL GetDeptBudget(10, @budget);
SELECT @budget;
```

**Benefits of Stored Procedures:**
- Reduce network traffic (one call instead of many)
- Precompiled → faster execution
- Enforce business rules at the database level
- Security — grant EXECUTE permission without exposing underlying tables

**Drawbacks:**
- Hard to test, debug, and version control
- Business logic split between application and database (maintenance headache)
- Less portable across different database vendors

### Triggers

A **trigger** is a procedure that automatically executes **in response to specific events** (INSERT, UPDATE, DELETE) on a table.

```sql
-- Log every salary change
CREATE TABLE salary_audit (
    audit_id   INT AUTO_INCREMENT PRIMARY KEY,
    employee_id INT,
    old_salary  DECIMAL(10,2),
    new_salary  DECIMAL(10,2),
    changed_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    changed_by  VARCHAR(100)
);

CREATE TRIGGER salary_change_audit
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary <> NEW.salary THEN
        INSERT INTO salary_audit (employee_id, old_salary, new_salary, changed_by)
        VALUES (NEW.id, OLD.salary, NEW.salary, CURRENT_USER());
    END IF;
END;
```

**Trigger timing:**
- `BEFORE` - Fires before the event (can validate/modify data)
- `AFTER` - Fires after the event (for logging, cascades)

**Trigger events:** `INSERT`, `UPDATE`, `DELETE`

**Use cases for triggers:**
- Audit logging (who changed what and when)
- Enforcing complex business rules
- Automatically maintaining summary tables
- Validating data beyond simple constraints

**Drawbacks of triggers:**
- Hidden behavior — code runs invisibly, hard to debug
- Performance overhead — fire on every operation
- Circular triggers possible (trigger A fires trigger B which fires A)

### Comparison Table

| Feature | View | Materialized View | Stored Procedure | Trigger |
|---|---|---|---|---|
| Stores data | No | Yes | No | No |
| Called by | Query (SELECT) | Query (SELECT) | Application (CALL) | Automatically |
| When it runs | On access | On refresh | On explicit call | On table event |
| Best use | Simplify queries, security | Expensive report caching | Reusable business logic | Audit, auto-maintenance |

### Interview Questions - Stored Procedures, Views, Triggers

**Q: When would you use a view vs a stored procedure?**
> A view is for simplifying read queries and controlling data access. A stored procedure is for encapsulating complex multi-step logic that may include writes, conditionals, and loops.

**Q: What are the disadvantages of using triggers heavily?**
> Triggers run implicitly and invisibly to the caller, making debugging difficult. They add overhead to every INSERT/UPDATE/DELETE. Complex trigger chains are hard to maintain. Business logic in triggers is hard to test and version-control.

**Q: What is a materialized view and when would you use it?**
> A materialized view stores the result of a query physically on disk. It's stale until refreshed, but reads are extremely fast. Use it for expensive analytical queries that are run frequently but don't need real-time freshness.

---

> **Key Takeaways - Section 14**
> - Views simplify queries and enforce security; they don't store data.
> - Materialized views store data for fast reads — useful for expensive reports.
> - Stored procedures encapsulate logic but are hard to test and version-control.
> - Triggers automate responses to data changes — use sparingly due to hidden complexity.

---

## 15. NoSQL Basics

### What is NoSQL?

**NoSQL** (Not Only SQL) databases are non-relational databases designed for scalability, flexibility, and specific data models that don't fit neatly into tables.

### SQL vs NoSQL

| Feature | SQL (Relational) | NoSQL |
|---|---|---|
| Data model | Tables with rows and columns | Documents, key-value, graph, column-family |
| Schema | Fixed, enforced schema | Flexible or schema-less |
| Relationships | Joins across normalized tables | Denormalized, embedded, or application-level |
| ACID compliance | Yes, built-in | Varies (many offer eventual consistency) |
| Scalability | Primarily vertical | Primarily horizontal |
| Query language | Standardized SQL | Database-specific APIs |
| Best for | Complex queries, transactions | High scalability, flexible schemas, large volume |
| Examples | PostgreSQL, MySQL, Oracle | MongoDB, Redis, Cassandra, DynamoDB |

### Document Databases

- Store data as JSON/BSON documents
- Documents can have nested fields and arrays
- No rigid schema — each document can have different fields
- Examples: **MongoDB**, **CouchDB**, **Firestore**

```json
// Example document in MongoDB
{
  "_id": "user123",
  "name": "Alice",
  "email": "alice@example.com",
  "orders": [
    {"order_id": "o1", "amount": 250, "status": "shipped"},
    {"order_id": "o2", "amount": 150, "status": "pending"}
  ],
  "address": {
    "city": "New York",
    "zip": "10001"
  }
}
```

**When to use:**
- Content management (blog posts, articles)
- User profiles with varying attributes
- Product catalogs with different attributes per category
- Real-time analytics with nested data

### Key-Value Stores

- Simplest NoSQL model: a key maps to a value (any data type)
- Extremely fast reads and writes (O(1))
- Values are opaque — no querying by value content
- Examples: **Redis**, **DynamoDB**, **Memcached**

```
Key              Value
user:123:name  → "Alice"
user:123:email → "alice@example.com"
session:abc123 → { user_id: 123, expires: 1714000000 }
product:p1:stock → 150
```

**When to use:**
- Session storage
- Caching (frequently read data)
- Rate limiting
- Real-time leaderboards (Redis sorted sets)
- Feature flags

### Column-Family Databases

- Data stored by column groups (families) rather than rows
- Optimized for writes and time-series data
- Each row can have different columns
- Examples: **Apache Cassandra**, **HBase**

**When to use:**
- IoT sensor data
- Time-series analytics
- Write-heavy workloads at massive scale
- Large-scale distributed systems

### Graph Databases

- Store data as nodes (entities) and edges (relationships)
- Optimized for traversing relationships
- Examples: **Neo4j**, **Amazon Neptune**

**When to use:**
- Social networks (friends of friends)
- Recommendation engines
- Fraud detection (relationship patterns)
- Knowledge graphs

### When to Choose NoSQL Over SQL

Choose NoSQL when:
- Data structure is highly variable or evolving rapidly
- Need to scale horizontally to many servers
- Working with massive volumes of data (Big Data)
- High write throughput is required
- Data doesn't need complex joins or ACID transactions
- Data is naturally hierarchical (documents) or a graph

Choose SQL when:
- Data has clear relationships and consistent structure
- ACID transactions are required (banking, e-commerce checkout)
- Complex queries and reporting are needed
- Joins between related entities are frequent
- Team is experienced with SQL and relational design

### CAP Theorem

Distributed databases must choose between:
- **Consistency** - Every read gets the most recent write
- **Availability** - Every request gets a response (not necessarily the latest)
- **Partition Tolerance** - System continues working despite network partitions

A distributed system can guarantee at most **2 of the 3**. Since network partitions are inevitable in distributed systems, the real choice is CP (consistent but may be unavailable) vs AP (available but may be stale).

| System | Type | Prioritizes |
|---|---|---|
| MySQL | Relational | CA (single node) |
| PostgreSQL | Relational | CA (single node) |
| MongoDB | Document | CP or AP (configurable) |
| Cassandra | Column-family | AP |
| HBase | Column-family | CP |
| DynamoDB | Key-Value | AP (eventually consistent by default) |

### Interview Questions - NoSQL

**Q: When would you choose MongoDB over PostgreSQL?**
> I'd choose MongoDB when the data structure varies significantly between records, when I need to store nested/hierarchical data naturally without complex joins, and when the system needs to scale horizontally across many servers. I'd choose PostgreSQL when I need ACID transactions, complex relational queries, or consistent schema enforcement.

**Q: What is eventual consistency?**
> Eventual consistency means that if no new updates are made to data, all replicas will eventually converge to the same value. Reads may temporarily return stale data. This is common in highly available distributed systems that prioritize availability over immediate consistency.

**Q: What is the CAP theorem?**
> A distributed system can provide at most two of three guarantees: Consistency, Availability, and Partition Tolerance. Since network partitions are unavoidable, systems must choose between being consistent (CP) or highly available (AP) during a partition.

---

> **Key Takeaways - Section 15**
> - NoSQL = flexible schema, horizontal scaling, specific data models (document, key-value, graph).
> - Use SQL for ACID transactions, complex joins, consistent schemas.
> - Use NoSQL for high scalability, flexible/evolving schemas, and large volumes of data.
> - CAP theorem: distributed systems choose between consistency (CP) and availability (AP).

---

## 16. Common Interview Questions

### Fresh Graduate Level

**Q1: What is the difference between a primary key and a foreign key?**
> A primary key uniquely identifies each row in a table — it must be unique and not null. A foreign key is a column in one table that references the primary key of another table, establishing a relationship and enforcing referential integrity.

**Q2: What is SQL and what are its major types of commands?**
> SQL (Structured Query Language) is used to manage and query relational databases. Its command categories are: DDL (CREATE, ALTER, DROP), DML (INSERT, UPDATE, DELETE), DQL (SELECT), DCL (GRANT, REVOKE), and TCL (COMMIT, ROLLBACK).

**Q3: What is normalization and why is it important?**
> Normalization is the process of organizing a database to reduce redundancy and prevent anomalies. It ensures data is stored once, updated in one place, and remains consistent. The main normal forms are 1NF, 2NF, 3NF, and BCNF.

**Q4: What is the difference between INNER JOIN and LEFT JOIN?**
> INNER JOIN returns only rows with matching values in both tables. LEFT JOIN returns all rows from the left table, and matching rows from the right — unmatched rows show NULL for the right table's columns.

**Q5: What is an index and why is it used?**
> An index is a data structure (usually a B-Tree) that allows the database to find rows quickly without scanning the entire table. It speeds up SELECT queries at the cost of slightly slower INSERT/UPDATE/DELETE operations and extra storage.

**Q6: What are ACID properties?**
> ACID stands for Atomicity (all or nothing), Consistency (valid state transitions), Isolation (transactions don't interfere), and Durability (committed changes survive crashes). ACID ensures reliable transaction processing.

**Q7: What is the difference between DELETE, TRUNCATE, and DROP?**
> DELETE removes specific rows (DML, rollback-able, row-by-row, fires triggers). TRUNCATE removes all rows fast (DDL, usually not rollback-able, no triggers). DROP deletes the entire table structure and data permanently.

**Q8: What is a JOIN? How many types are there?**
> A JOIN combines rows from two or more tables based on a related column. Types: INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL OUTER JOIN, SELF JOIN, and CROSS JOIN.

**Q9: Can you have a NULL value in a primary key?**
> No. A primary key must be unique AND NOT NULL. This is a fundamental constraint — a null value cannot uniquely identify a row.

**Q10: What is a composite key?**
> A composite key is a primary key made up of two or more columns. Used when no single column uniquely identifies a row. Example: `(student_id, course_id)` in an enrollment table.

---

### Mid-Level Questions

**Q11: How does GROUP BY differ from PARTITION BY?**
> GROUP BY collapses multiple rows into one row per group and is used with aggregate functions. PARTITION BY is used with window functions — it divides rows into groups for calculation but does NOT collapse them; each row remains in the output.

**Q12: What is a correlated subquery and what are its performance implications?**
> A correlated subquery references a column from the outer query and re-executes for every row of the outer query. This makes it O(n × m) in complexity. For large tables, replace with a JOIN or a non-correlated subquery using aggregation.

**Q13: Explain the difference between clustered and non-clustered indexes.**
> A clustered index physically sorts and stores the table data in index key order — only one per table. A non-clustered index is a separate structure with pointers to the actual data rows — a table can have many non-clustered indexes.

**Q14: What are window functions and when would you use them?**
> Window functions perform calculations across a set of rows related to the current row without collapsing them. Use them for running totals, moving averages, row ranking within groups, and period-over-period comparisons.

**Q15: How do you find the second highest salary in a table?**

```sql
-- Method 1: Subquery
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: DENSE_RANK (handles ties correctly)
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked WHERE rnk = 2;

-- Method 3: LIMIT/OFFSET (MySQL)
SELECT DISTINCT salary FROM employees ORDER BY salary DESC LIMIT 1 OFFSET 1;
```

**Q16: What is the difference between optimistic and pessimistic locking?**
> Pessimistic locking acquires a lock on data at read time and holds it until the transaction ends, preventing conflicts at the cost of concurrency. Optimistic locking reads without locking and checks for conflicts only at write time using a version number. Optimistic is better for read-heavy low-conflict scenarios; pessimistic for high-conflict write-heavy systems.

**Q17: What is query optimization and how would you approach it?**
> Query optimization is the process of improving query performance. Approach: (1) EXPLAIN the query to see the execution plan; (2) look for full table scans; (3) add appropriate indexes; (4) avoid applying functions on indexed columns; (5) minimize SELECT *; (6) fix N+1 query problems; (7) use keyset pagination instead of OFFSET; (8) ensure statistics are updated.

**Q18: What is the difference between UNION and UNION ALL?**
> UNION combines result sets and removes duplicate rows (requires sorting/hashing). UNION ALL combines result sets and keeps all rows including duplicates. UNION ALL is faster — use it when you know there are no duplicates or don't care about them.

```sql
SELECT name FROM employees_2023
UNION                          -- removes duplicates (slower)
SELECT name FROM employees_2024;

SELECT name FROM employees_2023
UNION ALL                      -- keeps duplicates (faster)
SELECT name FROM employees_2024;
```

**Q19: Explain isolation levels and when you'd use SERIALIZABLE.**
> Isolation levels control how much one transaction is isolated from others. READ UNCOMMITTED allows dirty reads. READ COMMITTED prevents dirty reads. REPEATABLE READ also prevents non-repeatable reads. SERIALIZABLE prevents all anomalies by executing transactions as if serial. Use SERIALIZABLE for financial operations where absolute correctness is required, accepting the performance cost of maximum locking.

**Q20: How do you implement pagination efficiently in SQL?**
> Avoid LIMIT + large OFFSET (must read and discard N rows). Instead, use keyset/cursor-based pagination: remember the last seen ID and filter with `WHERE id > last_seen_id ORDER BY id LIMIT page_size`. This is O(log n) regardless of page depth.

---

> **Key Takeaways - Section 16**
> - Know the "classic" interview questions: second highest salary, JOIN differences, ACID, normalization.
> - Be ready to write SQL on a whiteboard or in a live editor.
> - Explain trade-offs — interviewers want to know you understand the "why", not just syntax.

---

## 17. Top 25 SQL Interview Problems

*Practice these on any SQL editor (DB Fiddle, SQLFiddle, LeetCode, HackerRank).*

### Schema Used

```sql
CREATE TABLE employees (
    id        INT PRIMARY KEY,
    name      VARCHAR(100),
    dept_id   INT,
    salary    DECIMAL(10,2),
    manager_id INT,
    hire_date DATE
);

CREATE TABLE departments (
    dept_id   INT PRIMARY KEY,
    dept_name VARCHAR(100)
);

CREATE TABLE orders (
    order_id    INT PRIMARY KEY,
    customer_id INT,
    amount      DECIMAL(10,2),
    order_date  DATE,
    status      VARCHAR(20)
);

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name        VARCHAR(100),
    email       VARCHAR(255),
    country     VARCHAR(50)
);
```

---

### Problem 1: Find the Second Highest Salary

```sql
-- Using DENSE_RANK (handles ties correctly)
SELECT salary AS SecondHighestSalary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2;
```

---

### Problem 2: Find the Nth Highest Salary (generalized)

```sql
-- Replace N with desired rank (e.g., N=3 for 3rd highest)
SELECT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = N;
```

---

### Problem 3: Find Duplicate Emails (or Any Duplicate Column)

```sql
SELECT email, COUNT(*) AS occurrence
FROM customers
GROUP BY email
HAVING COUNT(*) > 1;
```

---

### Problem 4: Employees Earning More Than Their Manager

```sql
SELECT e.name AS employee_name, e.salary AS employee_salary
FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

---

### Problem 5: Departments with No Employees

```sql
SELECT d.dept_name
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
WHERE e.id IS NULL;
```

---

### Problem 6: Top N Earners Per Department

```sql
-- Top 3 earners per department
SELECT name, dept_id, salary
FROM (
    SELECT name, dept_id, salary,
           DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk <= 3;
```

---

### Problem 7: Cumulative/Running Total

```sql
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders
ORDER BY order_date;
```

---

### Problem 8: Month-Over-Month Revenue Growth

```sql
WITH monthly AS (
    SELECT
        DATE_FORMAT(order_date, '%Y-%m') AS month,
        SUM(amount) AS revenue
    FROM orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
)
SELECT
    month,
    revenue,
    LAG(revenue) OVER (ORDER BY month) AS prev_revenue,
    ROUND((revenue - LAG(revenue) OVER (ORDER BY month)) /
          LAG(revenue) OVER (ORDER BY month) * 100, 2) AS growth_pct
FROM monthly;
```

---

### Problem 9: Find Employees Who Have Never Placed an Order (if employees were customers)

```sql
-- Customers who have never placed an order
SELECT c.customer_id, c.name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;
```

---

### Problem 10: Rank Employees by Salary Within Each Department

```sql
SELECT
    name,
    dept_id,
    salary,
    RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS salary_rank
FROM employees;
```

---

### Problem 11: Average Salary Per Department vs Company Average

```sql
SELECT
    dept_id,
    AVG(salary) AS dept_avg,
    AVG(AVG(salary)) OVER () AS company_avg
FROM employees
GROUP BY dept_id;
```

---

### Problem 12: Find All Employees and Their Department Names (including those without a department)

```sql
SELECT e.name, COALESCE(d.dept_name, 'No Department') AS department
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
```

---

### Problem 13: Find the Most Recent Order Per Customer

```sql
SELECT customer_id, order_id, amount, order_date
FROM (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rn
    FROM orders
) ranked
WHERE rn = 1;
```

---

### Problem 14: Count Employees Hired Each Year

```sql
SELECT
    YEAR(hire_date) AS hire_year,
    COUNT(*) AS employee_count
FROM employees
GROUP BY YEAR(hire_date)
ORDER BY hire_year;
```

---

### Problem 15: Find Employees Whose Salary is Above the Department Average

```sql
SELECT e.name, e.salary, e.dept_id
FROM employees e
JOIN (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
) dept_avg ON e.dept_id = dept_avg.dept_id
WHERE e.salary > dept_avg.avg_salary;
```

---

### Problem 16: Delete Duplicate Rows (keep only one)

```sql
-- Keep the row with the smallest id for each duplicate email
DELETE FROM customers
WHERE customer_id NOT IN (
    SELECT MIN(customer_id)
    FROM customers
    GROUP BY email
);
```

---

### Problem 17: Pivot: Count Employees per Department in a Single Row

```sql
SELECT
    SUM(CASE WHEN dept_id = 10 THEN 1 ELSE 0 END) AS dept_10,
    SUM(CASE WHEN dept_id = 20 THEN 1 ELSE 0 END) AS dept_20,
    SUM(CASE WHEN dept_id = 30 THEN 1 ELSE 0 END) AS dept_30
FROM employees;
```

---

### Problem 18: Find Customers Who Ordered in Consecutive Months

```sql
WITH monthly_orders AS (
    SELECT DISTINCT
        customer_id,
        DATE_FORMAT(order_date, '%Y-%m') AS order_month
    FROM orders
),
with_next AS (
    SELECT
        customer_id,
        order_month,
        LEAD(order_month) OVER (PARTITION BY customer_id ORDER BY order_month) AS next_month
    FROM monthly_orders
)
SELECT DISTINCT customer_id
FROM with_next
WHERE DATE_ADD(STR_TO_DATE(CONCAT(order_month, '-01'), '%Y-%m-%d'), INTERVAL 1 MONTH)
      = STR_TO_DATE(CONCAT(next_month, '-01'), '%Y-%m-%d');
```

---

### Problem 19: Find All Managers (employees who manage someone)

```sql
SELECT DISTINCT m.id, m.name
FROM employees e
JOIN employees m ON e.manager_id = m.id;
```

---

### Problem 20: Percentage of Total Sales Per Customer

```sql
SELECT
    customer_id,
    SUM(amount) AS customer_total,
    SUM(amount) / SUM(SUM(amount)) OVER () * 100 AS percentage_of_total
FROM orders
GROUP BY customer_id
ORDER BY percentage_of_total DESC;
```

---

### Problem 21: Find the Employee with the Highest Salary in Each Department

```sql
SELECT dept_id, name, salary
FROM (
    SELECT dept_id, name, salary,
           ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
    FROM employees
) ranked
WHERE rn = 1;
```

---

### Problem 22: Find Orders Placed on the Same Day as Another Order by the Same Customer

```sql
SELECT DISTINCT o1.order_id, o1.customer_id, o1.order_date
FROM orders o1
JOIN orders o2
  ON o1.customer_id = o2.customer_id
  AND o1.order_date = o2.order_date
  AND o1.order_id <> o2.order_id;
```

---

### Problem 23: Total Revenue by Country

```sql
SELECT c.country, SUM(o.amount) AS total_revenue
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.country
ORDER BY total_revenue DESC;
```

---

### Problem 24: Find Employees Hired in the Last 90 Days

```sql
SELECT name, hire_date
FROM employees
WHERE hire_date >= CURDATE() - INTERVAL 90 DAY;
```

---

### Problem 25: Recursive CTE — Employee Hierarchy

```sql
WITH RECURSIVE hierarchy AS (
    -- Start with top-level (no manager)
    SELECT id, name, manager_id, 0 AS depth, CAST(name AS CHAR(1000)) AS path
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT e.id, e.name, e.manager_id, h.depth + 1,
           CONCAT(h.path, ' > ', e.name)
    FROM employees e
    JOIN hierarchy h ON e.manager_id = h.id
)
SELECT depth, name, path FROM hierarchy ORDER BY path;
```

---

> **Key Takeaways - Section 17**
> - Problems 1, 2, 6, 10 (ranking and N-th highest) appear in almost every SQL interview.
> - Practice window functions (ROW_NUMBER, RANK, DENSE_RANK, LAG, LEAD, SUM OVER).
> - Master LEFT JOIN + WHERE IS NULL for "find records in A not in B" patterns.
> - Understand how to use CTEs to break complex problems into readable steps.

---

## 18. Database Design Scenarios

### Scenario 1: E-Commerce Schema

**Requirements:**
- Customers can place orders
- Orders contain multiple products
- Products belong to categories
- Addresses stored for shipping
- Payment tracking needed

```sql
CREATE TABLE customers (
    customer_id  INT AUTO_INCREMENT PRIMARY KEY,
    name         VARCHAR(100) NOT NULL,
    email        VARCHAR(255) NOT NULL UNIQUE,
    phone        VARCHAR(20),
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE addresses (
    address_id   INT AUTO_INCREMENT PRIMARY KEY,
    customer_id  INT NOT NULL,
    line1        VARCHAR(255) NOT NULL,
    city         VARCHAR(100) NOT NULL,
    state        VARCHAR(100),
    zip          VARCHAR(20),
    country      VARCHAR(50) NOT NULL,
    is_default   BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE categories (
    category_id  INT AUTO_INCREMENT PRIMARY KEY,
    name         VARCHAR(100) NOT NULL,
    parent_id    INT,  -- self-referencing for subcategories
    FOREIGN KEY (parent_id) REFERENCES categories(category_id)
);

CREATE TABLE products (
    product_id   INT AUTO_INCREMENT PRIMARY KEY,
    name         VARCHAR(255) NOT NULL,
    description  TEXT,
    price        DECIMAL(10,2) NOT NULL CHECK (price >= 0),
    stock_qty    INT NOT NULL DEFAULT 0 CHECK (stock_qty >= 0),
    category_id  INT NOT NULL,
    is_active    BOOLEAN DEFAULT TRUE,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(category_id),
    INDEX idx_category (category_id),
    INDEX idx_price (price)
);

CREATE TABLE orders (
    order_id     INT AUTO_INCREMENT PRIMARY KEY,
    customer_id  INT NOT NULL,
    address_id   INT NOT NULL,
    status       ENUM('pending','confirmed','shipped','delivered','cancelled') DEFAULT 'pending',
    total_amount DECIMAL(10,2) NOT NULL,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (address_id) REFERENCES addresses(address_id),
    INDEX idx_customer (customer_id),
    INDEX idx_status (status)
);

CREATE TABLE order_items (
    item_id      INT AUTO_INCREMENT PRIMARY KEY,
    order_id     INT NOT NULL,
    product_id   INT NOT NULL,
    quantity     INT NOT NULL CHECK (quantity > 0),
    unit_price   DECIMAL(10,2) NOT NULL,  -- price at time of purchase
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

CREATE TABLE payments (
    payment_id   INT AUTO_INCREMENT PRIMARY KEY,
    order_id     INT NOT NULL UNIQUE,  -- one payment per order
    method       ENUM('credit_card','paypal','bank_transfer') NOT NULL,
    status       ENUM('pending','completed','failed','refunded') DEFAULT 'pending',
    amount       DECIMAL(10,2) NOT NULL,
    paid_at      TIMESTAMP,
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);
```

**Key design decisions:**
- `unit_price` stored in `order_items` (not fetched from products) — preserves historical price
- `parent_id` in categories enables unlimited subcategory nesting
- `ENUM` for status columns — restricts to valid values
- All foreign keys are indexed
- Soft delete not shown for brevity — add `deleted_at` for products/customers in production

---

### Scenario 2: Social Media Schema

**Requirements:**
- Users can follow each other
- Users can create posts
- Posts can be liked and commented on
- Hashtags on posts

```sql
CREATE TABLE users (
    user_id      INT AUTO_INCREMENT PRIMARY KEY,
    username     VARCHAR(50) NOT NULL UNIQUE,
    email        VARCHAR(255) NOT NULL UNIQUE,
    bio          TEXT,
    avatar_url   VARCHAR(500),
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Self-referencing M:N for following
CREATE TABLE follows (
    follower_id  INT NOT NULL,
    following_id INT NOT NULL,
    followed_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (follower_id, following_id),
    FOREIGN KEY (follower_id)  REFERENCES users(user_id),
    FOREIGN KEY (following_id) REFERENCES users(user_id),
    CHECK (follower_id <> following_id)  -- can't follow yourself
);

CREATE TABLE posts (
    post_id      INT AUTO_INCREMENT PRIMARY KEY,
    user_id      INT NOT NULL,
    content      TEXT NOT NULL,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at   TIMESTAMP NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    INDEX idx_user (user_id),
    INDEX idx_created (created_at)
);

CREATE TABLE likes (
    user_id      INT NOT NULL,
    post_id      INT NOT NULL,
    liked_at     TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id, post_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (post_id) REFERENCES posts(post_id)
);

CREATE TABLE comments (
    comment_id   INT AUTO_INCREMENT PRIMARY KEY,
    post_id      INT NOT NULL,
    user_id      INT NOT NULL,
    parent_id    INT,  -- for nested comments/replies
    content      TEXT NOT NULL,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id)   REFERENCES posts(post_id),
    FOREIGN KEY (user_id)   REFERENCES users(user_id),
    FOREIGN KEY (parent_id) REFERENCES comments(comment_id)
);

CREATE TABLE hashtags (
    hashtag_id   INT AUTO_INCREMENT PRIMARY KEY,
    tag          VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE post_hashtags (
    post_id      INT NOT NULL,
    hashtag_id   INT NOT NULL,
    PRIMARY KEY (post_id, hashtag_id),
    FOREIGN KEY (post_id)    REFERENCES posts(post_id),
    FOREIGN KEY (hashtag_id) REFERENCES hashtags(hashtag_id)
);
```

**Common queries on this schema:**

```sql
-- Get a user's feed (posts from people they follow)
SELECT p.post_id, p.content, u.username, p.created_at
FROM posts p
JOIN users u ON p.user_id = u.user_id
JOIN follows f ON p.user_id = f.following_id
WHERE f.follower_id = 42
  AND p.deleted_at IS NULL
ORDER BY p.created_at DESC
LIMIT 20;

-- Get post like count and comment count
SELECT p.post_id, p.content,
       COUNT(DISTINCT l.user_id) AS like_count,
       COUNT(DISTINCT c.comment_id) AS comment_count
FROM posts p
LEFT JOIN likes l    ON p.post_id = l.post_id
LEFT JOIN comments c ON p.post_id = c.post_id
WHERE p.post_id = 100
GROUP BY p.post_id, p.content;
```

---

### Scenario 3: Task Management Schema

**Requirements:**
- Users belong to organizations/teams
- Teams have projects
- Projects have tasks
- Tasks can be assigned, have due dates, status, and priority
- Tasks can have subtasks
- Comments on tasks

```sql
CREATE TABLE organizations (
    org_id       INT AUTO_INCREMENT PRIMARY KEY,
    name         VARCHAR(255) NOT NULL,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE users (
    user_id      INT AUTO_INCREMENT PRIMARY KEY,
    org_id       INT NOT NULL,
    name         VARCHAR(100) NOT NULL,
    email        VARCHAR(255) NOT NULL UNIQUE,
    role         ENUM('admin','manager','member') DEFAULT 'member',
    FOREIGN KEY (org_id) REFERENCES organizations(org_id)
);

CREATE TABLE projects (
    project_id   INT AUTO_INCREMENT PRIMARY KEY,
    org_id       INT NOT NULL,
    name         VARCHAR(255) NOT NULL,
    description  TEXT,
    owner_id     INT NOT NULL,
    status       ENUM('active','completed','archived') DEFAULT 'active',
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (org_id)    REFERENCES organizations(org_id),
    FOREIGN KEY (owner_id)  REFERENCES users(user_id)
);

CREATE TABLE tasks (
    task_id      INT AUTO_INCREMENT PRIMARY KEY,
    project_id   INT NOT NULL,
    parent_id    INT,  -- for subtasks (self-referencing)
    title        VARCHAR(500) NOT NULL,
    description  TEXT,
    assignee_id  INT,
    created_by   INT NOT NULL,
    status       ENUM('todo','in_progress','review','done') DEFAULT 'todo',
    priority     ENUM('low','medium','high','urgent') DEFAULT 'medium',
    due_date     DATE,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (project_id)  REFERENCES projects(project_id),
    FOREIGN KEY (parent_id)   REFERENCES tasks(task_id),
    FOREIGN KEY (assignee_id) REFERENCES users(user_id),
    FOREIGN KEY (created_by)  REFERENCES users(user_id),
    INDEX idx_project (project_id),
    INDEX idx_assignee (assignee_id),
    INDEX idx_status (status),
    INDEX idx_due_date (due_date)
);

CREATE TABLE task_comments (
    comment_id   INT AUTO_INCREMENT PRIMARY KEY,
    task_id      INT NOT NULL,
    user_id      INT NOT NULL,
    content      TEXT NOT NULL,
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (task_id)  REFERENCES tasks(task_id),
    FOREIGN KEY (user_id)  REFERENCES users(user_id)
);
```

**Common queries:**

```sql
-- Tasks due this week, grouped by assignee
SELECT u.name AS assignee, COUNT(*) AS task_count
FROM tasks t
JOIN users u ON t.assignee_id = u.user_id
WHERE t.due_date BETWEEN CURDATE() AND CURDATE() + INTERVAL 7 DAY
  AND t.status NOT IN ('done')
GROUP BY u.user_id, u.name
ORDER BY task_count DESC;

-- Overdue tasks per project
SELECT p.name AS project, COUNT(*) AS overdue_count
FROM tasks t
JOIN projects p ON t.project_id = p.project_id
WHERE t.due_date < CURDATE()
  AND t.status NOT IN ('done')
GROUP BY p.project_id, p.name;
```

---

> **Key Takeaways - Section 18**
> - Store price at time of order in order_items, not reference from products.
> - Self-referencing foreign keys handle hierarchical data (categories, comments, tasks, org chart).
> - M:N relationships use junction tables with composite PKs.
> - Always add indexes on foreign key columns and frequently filtered columns.

---

## 19. Performance Tuning Checklist

Use this checklist when diagnosing or reviewing slow database queries and schemas.

### Query Level

- [ ] **Use EXPLAIN / EXPLAIN ANALYZE** to view the execution plan
- [ ] **Eliminate full table scans** on large tables — add indexes
- [ ] **Avoid SELECT \*** — select only needed columns
- [ ] **No functions on indexed columns** in WHERE (`WHERE salary > 50000` not `WHERE salary/12 > 4000`)
- [ ] **No implicit type conversions** (compare same types)
- [ ] **Use EXISTS instead of COUNT** for existence checks
- [ ] **Replace OFFSET pagination with keyset pagination** for deep pages
- [ ] **Avoid OR on indexed columns** — rewrite as UNION if possible
- [ ] **Filter early** — apply WHERE conditions before JOINs when possible
- [ ] **Use covering indexes** for frequently run queries

### Index Level

- [ ] **Index all foreign key columns**
- [ ] **Index columns used in WHERE, JOIN ON, ORDER BY, GROUP BY**
- [ ] **Check index usage** — unused indexes waste write performance
- [ ] **Composite index column order** matches query patterns (leftmost prefix rule)
- [ ] **Don't over-index** write-heavy tables (each index slows INSERT/UPDATE/DELETE)
- [ ] **Consider partial indexes** for selective conditions (e.g., `WHERE status = 'active'`)

### Schema Level

- [ ] **Appropriate data types** — use INT not VARCHAR for IDs, DATE not VARCHAR for dates
- [ ] **NOT NULL constraints** where applicable — NULLs complicate queries and indexes
- [ ] **Normalized to 3NF** for OLTP systems
- [ ] **Soft deletes** where audit trails matter
- [ ] **Archival strategy** for old data (partition or archive old rows)

### Transaction Level

- [ ] **Keep transactions short** — long transactions hold locks and cause contention
- [ ] **Appropriate isolation level** — don't use SERIALIZABLE when READ COMMITTED suffices
- [ ] **Commit or rollback promptly** after operations
- [ ] **Handle deadlocks** in application code with retry logic
- [ ] **Use connection pooling** — avoid opening a new connection per request

### Infrastructure Level

- [ ] **Update statistics** regularly (`ANALYZE TABLE`)
- [ ] **Monitor slow query logs** and address top offenders first
- [ ] **Read replicas** for heavy read workloads
- [ ] **Connection pooling** (PgBouncer for PostgreSQL, ProxySQL for MySQL)
- [ ] **Caching layer** (Redis) for hot, frequently read data
- [ ] **Database partitioning** for very large tables (partition by date, region)

---

## 20. Final Revision Cheat Sheet

### SQL Commands Quick Reference

```sql
-- DDL
CREATE TABLE t (col TYPE CONSTRAINT);
ALTER TABLE t ADD/MODIFY/DROP COLUMN col TYPE;
DROP TABLE t;
TRUNCATE TABLE t;

-- DML
INSERT INTO t (col1, col2) VALUES (v1, v2);
UPDATE t SET col = val WHERE condition;
DELETE FROM t WHERE condition;

-- DQL
SELECT col1, col2 FROM t
WHERE condition
GROUP BY col
HAVING agg_condition
ORDER BY col ASC|DESC
LIMIT n OFFSET m;

-- Joins
SELECT * FROM a INNER JOIN b ON a.id = b.id;
SELECT * FROM a LEFT JOIN b ON a.id = b.id;
SELECT * FROM a RIGHT JOIN b ON a.id = b.id;
SELECT * FROM a FULL OUTER JOIN b ON a.id = b.id;
SELECT * FROM a CROSS JOIN b;
SELECT * FROM a SELF_JOIN a2 ON a.manager_id = a2.id;

-- CTEs
WITH cte AS (SELECT ...) SELECT * FROM cte;

-- Window Functions
ROW_NUMBER() OVER (PARTITION BY col ORDER BY col)
RANK()       OVER (PARTITION BY col ORDER BY col)
DENSE_RANK() OVER (PARTITION BY col ORDER BY col)
SUM(col)     OVER (PARTITION BY col ORDER BY col)
LAG(col, 1)  OVER (ORDER BY col)
LEAD(col, 1) OVER (ORDER BY col)

-- Transactions
BEGIN TRANSACTION;
SAVEPOINT sp_name;
ROLLBACK TO SAVEPOINT sp_name;
COMMIT;
ROLLBACK;
```

---

### Key Concept Quick Reference

| Concept | One-Line Summary |
|---|---|
| Primary Key | Unique + Not Null + one per table |
| Foreign Key | References PK of another table |
| INNER JOIN | Matching rows only |
| LEFT JOIN | All left + matching right (NULLs on right) |
| FULL OUTER JOIN | All rows from both tables |
| WHERE | Filters rows BEFORE grouping |
| HAVING | Filters groups AFTER GROUP BY |
| Subquery | Query inside a query |
| CTE | Named temporary query result (WITH clause) |
| Window Function | Aggregate without collapsing rows |
| ROW_NUMBER | Unique sequential rank |
| RANK | Rank with gaps after ties |
| DENSE_RANK | Rank without gaps after ties |
| 1NF | Atomic values, no repeating groups |
| 2NF | No partial dependency on composite key |
| 3NF | No transitive dependency |
| Clustered Index | Data physically stored in index order (1 per table) |
| Non-Clustered Index | Separate structure with row pointers (many per table) |
| Atomicity | All or nothing |
| Consistency | Valid state transitions |
| Isolation | Transactions don't interfere |
| Durability | Committed = permanent |
| Dirty Read | Reading uncommitted data |
| Non-Repeatable Read | Same row, different value on re-read |
| Phantom Read | Different set of rows on re-query |
| Optimistic Locking | Check version at write time |
| Pessimistic Locking | Lock at read time |
| Denormalization | Add redundancy for faster reads |
| N+1 Problem | 1 query + N queries per row = fix with JOIN |

---

### SQL Query Writing Template

When asked to write a SQL query in an interview, structure your thinking:

```
1. IDENTIFY: What table(s) do I need? → FROM / JOIN
2. FILTER: Which rows? → WHERE
3. GROUP: Do I need aggregation? → GROUP BY + aggregate function
4. FILTER GROUPS: Condition on aggregated values? → HAVING
5. SELECT: Which columns and expressions? → SELECT
6. SORT: Required order? → ORDER BY
7. LIMIT: Top N or pagination? → LIMIT / OFFSET
```

---

### Common SQL Patterns

```sql
-- Top N per group
WITH ranked AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY group_col ORDER BY sort_col DESC) AS rn
    FROM table_name
)
SELECT * FROM ranked WHERE rn <= N;

-- Records in A not in B
SELECT * FROM a LEFT JOIN b ON a.id = b.a_id WHERE b.a_id IS NULL;
-- OR
SELECT * FROM a WHERE id NOT IN (SELECT a_id FROM b);
-- OR
SELECT * FROM a WHERE NOT EXISTS (SELECT 1 FROM b WHERE b.a_id = a.id);

-- Duplicates
SELECT col, COUNT(*) FROM table GROUP BY col HAVING COUNT(*) > 1;

-- Running total
SELECT col, SUM(amount) OVER (ORDER BY date_col) AS running_total FROM table;

-- Period over period
SELECT col, LAG(metric) OVER (ORDER BY period) AS prev_period FROM table;

-- Existence check (fast)
SELECT CASE WHEN EXISTS (SELECT 1 FROM t WHERE condition) THEN 'Yes' ELSE 'No' END;
```

---

### ACID Acronym Memory Aid

```
A → Atomicity     → "All or Nothing" (bank transfer either completes or reverts)
C → Consistency   → "Constraints Hold" (balance can't go negative)
I → Isolation     → "Independence" (transactions don't see each other's in-progress changes)
D → Durability    → "Disk-persisted" (committed = survives crash)
```

---

### Isolation Levels Memory Aid (Most → Least Permissive)

```
READ UNCOMMITTED → "I'll read anything, even dirty laundry"
READ COMMITTED   → "Only read clean (committed) data"        ← Most common default
REPEATABLE READ  → "What I read stays the same all transaction"
SERIALIZABLE     → "One at a time, like taking a number"      ← Safest, slowest
```

---

### Interview Red Flags to Avoid

- Using `SELECT *` in production queries
- Forgetting to add indexes on foreign keys
- Using `OFFSET` for deep pagination
- Applying functions on indexed columns in WHERE
- Writing correlated subqueries when a JOIN would work
- Using `TRUNCATE` when you meant `DELETE` (or vice versa — know the difference)
- Ignoring NULL handling (`= NULL` instead of `IS NULL`)
- Not handling deadlocks in application code

---

*End of DBMS & SQL Interview Study Guide*

---

> **Study Strategy**
> 1. Read sections 1-6 for foundational SQL mastery.
> 2. Study sections 7-11 for system design and architecture questions.
> 3. Practice all 25 problems in Section 17 in a live SQL editor.
> 4. Review sections 18-20 the day before your interview.
> 5. Know how to write the "Top N per group" pattern without looking it up — it appears in 80% of SQL interviews.
