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
  - `Written Order:` SELECT, TOP, DISTINCT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT
  - `Execution Order:` FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY, TOP/LIMIT
- Filtering with `logical operators` (AND, OR, NOT, BETWEEN, IN, LIKE)
- Aliases for columns (mostly optional) and tables (mandatory with joins)

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

### Miscellaneous

#### `DELETE vs TRUNCATE:`

**`DELETE`** removes specific rows from a table using a condition. **`TRUNCATE`** removes all rows from a table.

#### `UNION vs UNION ALL:`

`UNION` and `UNION ALL` are used to combine the result sets of two or more SELECT statements. `UNION` removes duplicate rows from the combined result set. `UNION ALL` includes all rows, including duplicates.

