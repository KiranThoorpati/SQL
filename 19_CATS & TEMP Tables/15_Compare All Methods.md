# SQL Query Methods: A Simple Guide for Beginners

## Introduction

When working with databases, we often need to analyze data in complex ways. SQL gives us several different methods to organize our queries. Let's explore five important techniques and understand when to use each one!

---

## The Five Methods

### 1. **Subqueries**
A query inside another query.

**Example:**
```sql
-- Find employees who earn more than the average salary
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Expected Output:**
```
name          | salary
--------------|--------
John Smith    | 75000
Sarah Jones   | 82000
Mike Wilson   | 68000
```

---

### 2. **CTE (Common Table Expressions)**
A temporary named result that you can use multiple times in the same query.

**Example:**
```sql
-- Calculate average salary by department
WITH dept_avg AS (
    SELECT department, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
)
SELECT e.name, e.department, e.salary, d.avg_salary
FROM employees e
JOIN dept_avg d ON e.department = d.department;
```

**Expected Output:**
```
name          | department | salary | avg_salary
--------------|------------|--------|------------
Alice Brown   | Sales      | 55000  | 52000
Bob Green     | Sales      | 49000  | 52000
Carol White   | IT         | 72000  | 70000
```

---

### 3. **Views**
A saved query that acts like a virtual table.

**Example:**
```sql
-- Create a view for high-performing employees
CREATE VIEW top_employees AS
SELECT name, department, salary
FROM employees
WHERE salary > 60000;

-- Use the view
SELECT * FROM top_employees;
```

**Expected Output:**
```
name          | department | salary
--------------|------------|--------
John Smith    | IT         | 75000
Sarah Jones   | Marketing  | 82000
Carol White   | IT         | 72000
```

---

### 4. **Temporary Tables**
A real table that exists only during your session.

**Example:**
```sql
-- Create a temporary table
CREATE TEMPORARY TABLE sales_summary AS
SELECT product, SUM(quantity) as total_sold
FROM sales
GROUP BY product;

-- Query the temporary table
SELECT * FROM sales_summary;
```

**Expected Output:**
```
product       | total_sold
--------------|------------
Laptop        | 45
Mouse         | 120
Keyboard      | 89
```

---

### 5. **CTAS (Create Table As Select)**
Creates a permanent new table from a query.

**Example:**
```sql
-- Create a permanent table with query results
CREATE TABLE monthly_summary AS
SELECT 
    MONTH(order_date) as month,
    COUNT(*) as total_orders,
    SUM(amount) as total_sales
FROM orders
GROUP BY MONTH(order_date);

-- Query the new table
SELECT * FROM monthly_summary;
```

**Expected Output:**
```
month | total_orders | total_sales
------|--------------|-------------
1     | 245          | 125000
2     | 289          | 142500
3     | 312          | 158900
```

---

## Comparison Chart

| Feature | Subquery | CTE | View | Temp Table | CTAS |
|---------|----------|-----|------|------------|------|
| **Storage** | Memory | Memory | No storage | Disk | Disk |
| **Lifetime** | Query only | Query only | Permanent | Session only | Permanent |
| **Reusability** | Low | Medium | High | Medium | High |
| **Data Freshness** | Always fresh | Always fresh | Always fresh | Can be stale | Can be stale |
| **Use in multiple queries** | No | No | Yes | Yes | Yes |

---

## When to Use Each Method

**Use Subqueries when:**
- You need a simple one-time calculation
- The logic is used in just one place

**Use CTEs when:**
- You need to use the same logic multiple times in one query
- You want cleaner, more readable code

**Use Views when:**
- Many people need to access the same logic
- You want always up-to-date data
- The query runs fast

**Use Temporary Tables when:**
- You need intermediate results during one work session
- You're doing step-by-step data processing

**Use CTAS when:**
- A query is very slow (takes many minutes)
- You can accept slightly outdated data
- You need a permanent backup of results

---

## Simple Example Showing the Difference

Let's say we have a students table:

```sql
-- Original table
students: id | name | grade | score
```

**Same goal, different methods:**

```sql
-- METHOD 1: Subquery
SELECT name, score
FROM students
WHERE score > (SELECT AVG(score) FROM students);

-- METHOD 2: CTE
WITH avg_score AS (
    SELECT AVG(score) as average FROM students
)
SELECT name, score
FROM students, avg_score
WHERE score > average;

-- METHOD 3: View
CREATE VIEW above_average AS
SELECT name, score
FROM students
WHERE score > (SELECT AVG(score) FROM students);

SELECT * FROM above_average;
```

All three methods give the same result, but they work differently behind the scenes!

---

## Key Takeaways

1. **Subqueries and CTEs** are temporary and disappear after the query runs
2. **Views** are like saved queries - they always show fresh data
3. **Temporary tables** last for your work session
4. **CTAS** creates permanent tables that store actual data
5. Choose the method based on: How long you need it? How often will you use it? Does speed matter?

---

This guide gives you the foundation to organize complex SQL queries like a professional data analyst!
