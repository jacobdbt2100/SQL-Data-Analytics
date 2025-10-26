# SQL for Data Analysts — 4-Week Roadmap

A practical 4-week SQL learning plan focused on querying, cleaning, and analysing data.  
All examples use standard **PostgreSQL / MySQL-friendly** syntax that can easily adapt to most SQL dialects.

---

## Week 1 — SQL Basics & Table Foundations
**Goal:** Understand databases, tables, and how to retrieve and filter data.

### 🔹 Topics
- What is SQL and how databases are structured  
- Creating and inserting into tables  
- SELECT, WHERE, and ORDER BY clauses  
- Filtering with logical operators (AND, OR, NOT, BETWEEN, IN, LIKE)  
- Aliases for columns and tables  

### 💻 Example Queries
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

## Week 2 — Aggregations & Group Analysis

Goal: Summarise data to find patterns and insights.

🔹 Topics

COUNT, SUM, AVG, MIN, MAX

GROUP BY and HAVING

DISTINCT and aggregate filtering

Simple derived columns using arithmetic and CASE

💻 **Example Queries**:

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
