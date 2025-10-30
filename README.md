# SQL for Data Analysts — 4-Week Roadmap

## Week 1 — SQL Basics & Database Foundations

- **SQL definition** and how **databases** are structured

  **SQL (Structured Query Language)** is a standard programming language used to communicate with **`relational databases`** — systems that store data in tables (rows and columns). With SQL, you can:
  - Create databases and tables
  - Insert data into tables
  - Query data (ask questions to retrieve specific information)
  - Update existing data
  - Delete data
  - Control access (security)

  A **`database`** is an organized collection of data stored and accessed electronically. It provides a way to store, organize, and retrieve large amounts of data efficiently.

- **SQL Clauses**
  - `Query Logical Written Order:` SELECT, TOP, DISTINCT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT
  - `Query Logical Execution Order:`

| **Order** | **Clause**                       | **Purpose**                                    |
| --------- | -------------------------------- | -----------------------------------------------|
| 1️         | **FROM**                         | Identify the table(s) or source(s) of data.    |
| 2️         | **ON** *(if JOIN used)*          | Define join conditions between tables.         |
| 3️         | **JOIN**                         | Combine data from multiple tables.             |
| 4️         | **WHERE**                        | Filter rows before grouping.                   |
| 5️         | **GROUP BY**                     | Group rows that share the same values.         |
| 6️         | **HAVING**                       | Filter groups after aggregation.               |
| 7️         | **SELECT**                       | Choose which columns or expressions to return. |
| 8️         | **DISTINCT** *(if used)*         | Remove duplicate rows from the result.         |
| 9️         | **ORDER BY**                     | Sort the final results.                        |
| 10        | **TOP / LIMIT / OFFSET / FETCH** | Restrict the number of rows returned.          |

- Filtering with `logical operators` (AND, OR, NOT, BETWEEN, IN, LIKE)
- Aliases for columns (mostly optional) and tables (mandatory with joins)
  - `ALIAS command` in SQL is the name that can be given to any table or a column.

**Example Queries**:
```sql
-- Create a simple table
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    country VARCHAR(50),
    age INT
);

-- Insert sample data
INSERT INTO customers (name, country, age)
VALUES

('John Doe', 'Nigeria', 30),
('Mary Smith', 'Kenya', 25),
('Adewale Ogun', 'Nigeria', 41);

-- Retrieve and filter
SELECT name, country, age
FROM customers
WHERE country = 'Nigeria'
ORDER BY age DESC;
```

## Week 2 — Data Aggregation

- COUNT, SUM, AVG, MIN, MAX
- GROUP BY and HAVING
- DISTINCT and aggregate filtering
- Derived columns using CASE

**Example Queries**:

```sql
-- Count customers by country
SELECT country, COUNT(*) AS total_customers
FROM customers
GROUP BY country;

-- Average age of customers per country (filter countries with more than 1)
SELECT country, ROUND(AVG(age), 1) AS avg_age
FROM customers
GROUP BY country
HAVING COUNT(*) > 1;

-- Conditional logic with CASE
SELECT 
    name,
    age,
    CASE 
        WHEN age < 30 THEN 'Young'
        WHEN age BETWEEN 30 AND 45 THEN 'Mid Age'
        ELSE 'Senior'
    END AS age_group
FROM customers;
```
## Week 3 — Data Cleaning, Joins & CTEs

- Data cleaning with TRIM, LOWER, UPPER, REPLACE
- Handling NULLs with COALESCE, IS NULL, IS NOT NULL
- JOINS in SQL
  
  A `join` is an operation used to combine rows from two or more tables based on related columns. It enables data retrieval from multiple tables simultaneously. Types: `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL JOIN`, `CROSS JOIN`, `SELF JOIN`.

- Subqueries and CTEs (Common Table Expressions)
  - A `subquery` is a query nested inside another query. A `correlated subquery` is a subquery that depends on the outer query. It runs once for each row returned by the outer query.
  - A `CTE` (Common Table Expression) is a temporary named result set that can be referenced within a SQL query. It makes complex queries easier to read and manage — especially when using subqueries or recursion. A `recursive CTE` is a CTE that refers to itself to handle hierarchical or sequential data, such as organization charts, family trees, or folder structures.

**Example Queries**:

```sql
-- Create an orders table
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10,2),
    order_date DATE
);

-- Join customers and orders
SELECT 
    c.name,
    o.order_id,
    o.amount,
    o.order_date
FROM customers AS c
LEFT JOIN orders AS o
ON c.customer_id = o.customer_id;

-- Use CTE to find top spenders
WITH total_spent AS (
    SELECT customer_id, SUM(amount) AS total_amount
    FROM orders
    GROUP BY customer_id
)
SELECT c.name, t.total_amount
FROM customers AS c
JOIN total_spent AS t
ON c.customer_id = t.customer_id
ORDER BY t.total_amount DESC;
```

## Week 4 — Query Optimization & Final Project

- Query performance basics (LIMIT, indexes conceptually, avoiding SELECT *)
- Joins with filters and aggregation
- Window functions (ROW_NUMBER, RANK, SUM OVER)
- Mini analytical **case studies** project

**Example Queries**

```sql
-- Using a window function to rank customers by total order amount
SELECT 
    c.name,
    SUM(o.amount) AS total_spent,
    RANK() OVER (ORDER BY SUM(o.amount) DESC) AS spending_rank
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.name;

-- Simple data quality check
SELECT *
FROM orders
WHERE amount IS NULL OR amount <= 0;

-- Top 3 customers per country by total spending
WITH country_rank AS (
    SELECT 
        c.country,
        c.name,
        SUM(o.amount) AS total_spent,
        ROW_NUMBER() OVER (PARTITION BY c.country ORDER BY SUM(o.amount) DESC) AS rnk
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.country, c.name
)
SELECT * 
FROM country_rank
WHERE rnk <= 3;
```

### MISCELLANEOUS

#### `DELETE vs TRUNCATE:`
`DELETE` removes specific rows from a table using a condition. `TRUNCATE` removes all rows from a table.

#### `UNION vs UNION ALL:`
`UNION` and `UNION ALL` are used to combine the result sets of two or more SELECT statements. `UNION` removes duplicate rows from the combined result set. `UNION ALL` includes all rows, including duplicates.

#### `Transaction:`
A `transaction` is a sequence of SQL statements that are executed as a single logical unit of work. It ensures data consistency and integrity by either committing all changes or rolling them back if an error occurs.

#### `Clustered vs Non-clustered Index:`
A `clustered Index` determines the physical order of data in a table. It changes the way the data is stored on disk and can be created on only one column. A table can have only one clustered index.

A `Non-clustered Index` does not affect the physical order of data in a table. It is stored separately and contains a pointer to the actual data. A table can have multiple non-clustered indexes.

#### `ACID in database transactions:`
`ACID` stands for Atomicity, Consistency, Isolation,and Durability. It is a set of properties that guarantee reliable processing of database transactions.
  - `Atomicity` ensures that a transaction is treated as a single unit of work, either all or none of the changes are applied.
  - `Consistency` ensures that a transaction brings the database from one valid state to another
  - `Isolation` ensures that concurrent transactions do not interfere with each other
  - `Durability` ensures that once a transaction is committed, its changes are permanent and survive system failures.

#### `Database vs Schema:`

| **Aspect**     | **Database**                                                                    | **Schema**                                                                                            |
| -------------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Definition** | A container for all data and objects such as **tables, views, and procedures**. | A container within a database that holds objects like **tables and views**, defining their ownership. |
| **Hierarchy**  | Higher level — contains schemas.                                                | Lower level — exists inside a database.                                                               |

`Why schemas are useful:`

  - They help organise database objects (e.g. separating sales and HR tables).
  - They control access — permissions can be granted per schema.
  - They prevent naming conflicts between objects.

#### `Temporary table vs Table variable:`

| **Aspect**     | **Temporary Table**                         | **Table Variable**                             |
| -------------- | ------------------------------------------- | ---------------------------------------------- |
| **Definition** | A table stored temporarily in the database. | A variable that holds table data in memory.    |
| **Scope**      | Exists for a session or transaction.        | Exists within a batch, procedure, or function. |
| **Storage**    | Stored in tempdb.                           | Primarily stored in memory.                    |
| **Lifetime**   | Dropped when session or transaction ends.   | Deallocated when its scope ends.               |

#### `Stored Procedure:`
Is a saved set of SQL commands that performs a specific task — like retrieving, inserting, updating, or deleting data — and can be executed repeatedly. Can be with or without `parameters`.

#### `View:`
A `view` is a virtual table based on the result of an SQLstatement. A `materialized view` is a physical copy of the view's result set stored in the database, which is updated periodically.

#### `IN vs EXISTS:`

| **Aspect**       | **IN**                                                                                    | **EXISTS**                                                                            |
| ---------------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Purpose**      | Checks if a value exists **within a list or subquery result**.                            | Checks if **any row** exists in a subquery that meets the condition.                  |
| **How it works** | Compares values directly.                                                                 | Tests for the **existence** of rows — stops checking once it finds one match.         |                       
#### `Data Warehouse:`
Is a large, centralized repository that stores and manages data from various sources designed for efficient analysis and reporting purposes.

#### `Primary key vs Candidate key:`
A `primary key` is a chosen candidate key that uniquely identifies a row in a table. A `candidate key` is a set of one or more columns that could potentially become the primary key.

#### `GRANT:`
The `GRANT statement` is used to grant specific permissions or privileges to users or roles in a database.

#### `COALESCE:`
The `COALESCE function` returns the first non-null expression from a list of expressions. It is often used to handle null values effectively.

#### `ROW_NUMBER():`
The `ROW_NUMBER() function` assigns a unique incremental number to each row in the result set.

#### `Some Purposes of SQL Functions:`
- Perform some calculations on the data
- Modify individual data items
- Manipulate output
- Format dates and numbers
- Convert data types

#### `SQL Commands:`
`SQL commands` are grouped based on what they do in a database. Here’s a simple breakdown:

1. **DDL (Data Definition Language):** `define`, `alter`, or `remove` database structures.

| **Command** | **Purpose**                                                |
| ----------- | ---------------------------------------------------------- |
| `CREATE`    | Creates a new database object (table, view, schema, etc.). |
| `ALTER`     | Modifies an existing object.                               |
| `DROP`      | Deletes an object permanently.                             |
| `TRUNCATE`  | Removes all data but keeps the table structure.            |
| `RENAME`    | Changes the name of a database object.                     |

2. **DML (Data Manipulation Language):** `manipulate data` stored in tables.

| **Command** | **Purpose**                                                                    |
| ----------- | ------------------------------------------------------------------------------ |
| `INSERT`    | Adds new records.                                                              |
| `UPDATE`    | Modifies existing records.                                                     |
| `DELETE`    | Removes specific records.                                                      |
| `MERGE`     | Combines `INSERT`, `UPDATE`, and `DELETE` in one statement (used for upserts). |

`MERGE` adds new rows or updates existing ones in a single step (used when matching data from two tables).

3. **DCL (Data Control Language):** `control` access and privileges.

| **Command** | **Purpose**               |
| ----------- | ------------------------- |
| `GRANT`     | Gives user permissions.   |
| `REVOKE`    | Removes user permissions. |

4. **TCL (Transaction Control Language):** `manage transactions` and maintain data integrity.

| **Command** | **Purpose**                                        |
| ----------- | -------------------------------------------------- |
| `COMMIT`    | Saves changes made in a transaction.               |
| `ROLLBACK`  | Reverses changes before commit.                    |
| `SAVEPOINT` | Sets a point to roll back to within a transaction. |

5. **DQL (Data Query Language):** `query` and retrieve data.

| **Command** | **Purpose**                             |
| ----------- | --------------------------------------- |
| `SELECT`    | Retrieves data from one or more tables. |

#### `SQL Summary:`

| **Category**                     | **Key Commands / Functions**                                                                                          | **Notes & Use Cases**                                          |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| **🔹 Basics**                    | `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`, `ORDER BY`, `LIMIT` / `TOP`, `DISTINCT`                              | Foundation of all SQL queries.                                 |
| **🔸 Operators**                 | `=`, `!=`, `<`, `>`, `<=`, `>=`, `BETWEEN`, `IN`, `NOT IN`, `LIKE`, `ILIKE`, `AND`, `OR`, `NOT`                       | Used to filter and compare values.                             |
| **🔹 Joins**                     | `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`, `SELF JOIN`, `CROSS JOIN`                                 | Combine rows from multiple tables based on a related column.   |
| **🔸 Aggregate Functions**       | `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`, `MEDIAN()`, `MODE()`, `STDDEV()`                                       | Summarise or aggregate data — often used with `GROUP BY`.      |
| **🔹 String Functions**          | `CONCAT()`, `REPLACE()`, `UPPER()`, `LOWER()`, `LTRIM()`, `RTRIM()`, `TRIM()`, `SUBSTRING()`, `LEN()` / `LENGTH()`    | Used for text manipulation and cleaning.                       |
| **🔸 Date & Time Functions**     | `GETDATE()`, `CURRENT_DATE`, `YEAR()`, `MONTH()`, `DAY()`, `DATEDIFF()`, `DATEADD()`, `DATE_TRUNC()`, `DATE_FORMAT()` | Handle and format date/time values.                            |
| **🔹 Cleaning & Transformation** | `CAST()`, `CONVERT()`, `COALESCE()`, `NULLIF()`, `CASE WHEN`, `IFNULL()`, `IIF()`, `LISTAGG()`                        | Data formatting, null handling, and conditional logic.         |
| **🔸 Set Operations**            | `UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT` / `MINUS`                                                                 | Combine or compare results from multiple queries.              |
| **🔹 Subqueries & CTEs**         | `WITH ... AS (...)`, nested `SELECT`                                                                                  | Used for reusing logic or breaking complex queries into steps. |
| **🔸 Window Functions**          | `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LEAD()`, `LAG()`, `SUM() OVER(...)`, `AVG() OVER(...)`                     | Perform calculations across rows without grouping.             |
| **🔹 Data Definition (DDL)**     | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME`                                                                       | Define or modify database structures (tables, views, etc.).    |
| **🔸 Data Manipulation (DML)**   | `INSERT`, `UPDATE`, `DELETE`, `MERGE`                                                                                 | Add, change, or remove data in tables.                         |
| **🔹 Data Control (DCL)**        | `GRANT`, `REVOKE`                                                                                                     | Manage user permissions and security.                          |
| **🔸 Transaction Control (TCL)** | `COMMIT`, `ROLLBACK`, `SAVEPOINT`                                                                                     | Manage transactions and ensure data integrity.                 |
| **🔹 Advanced SQL**              | `UDFs`, `Views`, `Indexes`, `Data Modeling`, `Recursive CTEs`                                                         | For performance, organisation, and hierarchical data.          |



















