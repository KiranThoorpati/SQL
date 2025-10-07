# SQL Subqueries - Complete Summary

## What is a Subquery?

A **subquery** is simply a **query inside another query**! It helps break big, complicated problems into smaller, easier steps.

**Think of it like:** Making a sandwich step-by-step instead of trying to do everything at once!

---

## Why Use Subqueries? 🎯

### 1. **Break Down Complex Problems**
Split difficult tasks into simple, manageable pieces.

```sql
-- Instead of one confusing query, break it into steps:
SELECT customer_name, total_spent
FROM (
    -- Step 1: Calculate totals (Subquery)
    SELECT customer_id, SUM(amount) AS total_spent
    FROM orders
    GROUP BY customer_id
) AS customer_totals
JOIN customers ON customers.customer_id = customer_totals.customer_id
WHERE total_spent > 1000;
```

**Expected Output:**
```
customer_name | total_spent
--------------|------------
John Smith    | 2500
Bob Wilson    | 1800
Alice Brown   | 1500
```

### 2. **Create Temporary Result Sets**
Prepare data in one step, use it in the next.

```sql
-- Prepare student averages, then filter
SELECT *
FROM (
    SELECT 
        student_name,
        AVG(score) AS avg_score
    FROM test_scores
    GROUP BY student_name
) AS averages
WHERE avg_score >= 80;
```

**Expected Output:**
```
student_name | avg_score
-------------|----------
Alice        | 92.5
Charlie      | 85.0
Emma         | 88.3
```

### 3. **Prepare Data Before Joining**
Calculate or filter before combining tables.

```sql
-- Calculate order counts, THEN join with customers
SELECT 
    c.customer_name,
    c.city,
    order_counts.total_orders
FROM customers c
LEFT JOIN (
    -- Prepare: Count orders per customer
    SELECT customer_id, COUNT(*) AS total_orders
    FROM orders
    GROUP BY customer_id
) AS order_counts ON c.customer_id = order_counts.customer_id;
```

**Expected Output:**
```
customer_name | city      | total_orders
--------------|-----------|-------------
John Smith    | New York  | 5
Mary Jones    | Chicago   | 3
Bob Wilson    | Boston    | 7
Anna Davis    | Miami     | NULL
```

### 4. **Dynamic Filtering**
Filter based on calculated values that change.

```sql
-- Find products above average price
SELECT product_name, price
FROM products
WHERE price > (
    SELECT AVG(price) FROM products  -- Dynamic average
);
```

**Expected Output:**
```
product_name | price
-------------|-------
Laptop       | 800
Monitor      | 300
Tablet       | 400
```

### 5. **Check Existence**
See if related data exists in other tables.

```sql
-- Find students who submitted homework
SELECT student_name, grade
FROM students s
WHERE EXISTS (
    SELECT 1
    FROM homework_submissions hs
    WHERE hs.student_id = s.student_id
);
```

**Expected Output:**
```
student_name | grade
-------------|-------
Alice        | 7
Charlie      | 7
Emma         | 8
Frank        | 6
```

### 6. **Row-by-Row Comparison**
Compare each row to its specific group.

```sql
-- Find students above their grade's average
SELECT 
    student_name,
    grade,
    score
FROM students s1
WHERE score > (
    SELECT AVG(score)
    FROM students s2
    WHERE s2.grade = s1.grade  -- Compare within same grade
);
```

**Expected Output:**
```
student_name | grade | score
-------------|-------|-------
Alice        | 7     | 95
Charlie      | 7     | 88
Emma         | 8     | 90
Bob          | 6     | 85
```

---

## All Subquery Types - Quick Reference

### By Location (Where You Use Them)

#### 1. In SELECT Clause
```sql
SELECT 
    product_name,
    price,
    (SELECT AVG(price) FROM products) AS avg_price
FROM products;
```

#### 2. In FROM Clause (Most Common!)
```sql
SELECT *
FROM (
    SELECT * FROM students WHERE score >= 70
) AS passing_students;
```

#### 3. In JOIN
```sql
SELECT c.customer_name, totals.order_count
FROM customers c
JOIN (
    SELECT customer_id, COUNT(*) AS order_count
    FROM orders GROUP BY customer_id
) AS totals ON c.customer_id = totals.customer_id;
```

#### 4. In WHERE Clause
```sql
SELECT * FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

---

### By Result Type

#### 1. Scalar (ONE Value)
```sql
SELECT AVG(score) FROM students;  -- Returns: 78.5
```

#### 2. Row (ONE Column, Multiple Rows)
```sql
SELECT customer_id FROM customers WHERE country = 'USA';
-- Returns: 1, 3, 5, 7
```

#### 3. Table (Multiple Rows & Columns)
```sql
SELECT student_name, grade, score FROM students;
-- Returns: Full table
```

---

### By Dependency

#### 1. Non-Correlated (Independent)
Runs **once** on its own.

```sql
-- Subquery runs once, result used for all rows
SELECT student_name, score
FROM students
WHERE score > (SELECT AVG(score) FROM students);
```

#### 2. Correlated (Dependent)
Runs **multiple times**, once per row.

```sql
-- Subquery runs for EACH customer
SELECT customer_name,
    (SELECT COUNT(*) FROM orders o 
     WHERE o.customer_id = c.customer_id) AS order_count
FROM customers c;
```

---

## Operators Used with Subqueries

### 1. Comparison Operators (=, >, <, >=, <=, !=)
Must return ONE value.

```sql
SELECT * FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

### 2. IN Operator
Check if value is in a list.

```sql
SELECT * FROM orders
WHERE customer_id IN (
    SELECT customer_id FROM customers WHERE country = 'USA'
);
```

### 3. ANY Operator
True if condition matches AT LEAST ONE value.

```sql
SELECT * FROM employees
WHERE salary > ANY (SELECT salary FROM employees WHERE department = 'Sales');
```

### 4. ALL Operator
True if condition matches EVERY value.

```sql
SELECT * FROM employees
WHERE salary > ALL (SELECT salary FROM employees WHERE department = 'Sales');
```

### 5. EXISTS Operator
Check if subquery returns any rows.

```sql
SELECT * FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```

---

## Complete Real-World Example

**Problem:** Find customers who spent more than the average, show their details and rank them.

```sql
-- Step 1: Calculate spending per customer (Subquery in FROM)
-- Step 2: Filter those above average (Subquery in WHERE)
-- Step 3: Rank them (Main query)

SELECT 
    customer_name,
    city,
    total_spent,
    RANK() OVER (ORDER BY total_spent DESC) AS spending_rank
FROM (
    -- Subquery: Calculate totals
    SELECT 
        c.customer_id,
        c.customer_name,
        c.city,
        (SELECT SUM(total_amount) 
         FROM orders o 
         WHERE o.customer_id = c.customer_id) AS total_spent
    FROM customers c
) AS customer_spending
WHERE total_spent > (
    -- Subquery: Calculate average
    SELECT AVG(total_amount) 
    FROM orders
)
ORDER BY spending_rank;
```

**Expected Output:**
```
customer_name | city      | total_spent | spending_rank
--------------|-----------|-------------|---------------
Bob Wilson    | Boston    | 3800        | 1
John Smith    | New York  | 2500        | 2
Alice Brown   | Seattle   | 1800        | 3
Mary Jones    | Chicago   | 1200        | 4
```

---

## Key Benefits of Subqueries

✅ **Break complex problems into simple steps**  
✅ **Make code easier to read and understand**  
✅ **Create temporary results without making permanent tables**  
✅ **Enable dynamic filtering**  
✅ **Check relationships between tables**  
✅ **Perform row-by-row comparisons**  

---

## Quick Decision Guide

**Need to compare to a single value?** → Use WHERE with scalar subquery  
**Need to check a list?** → Use IN operator  
**Need to prepare data before joining?** → Use subquery in FROM or JOIN  
**Need to add calculated column?** → Use subquery in SELECT  
**Need to check if related data exists?** → Use EXISTS  
**Need row-specific comparisons?** → Use correlated subquery  

---

## Key Takeaways

✅ **Subquery = Query inside another query**  
✅ **Break big problems into small steps**  
✅ **Can use in SELECT, FROM, JOIN, or WHERE**  
✅ **Different operators: IN, ANY, ALL, EXISTS**  
✅ **Two types: Non-correlated (fast) and Correlated (flexible)**  
✅ **Makes complex SQL manageable and readable**  

**Next Step:** Learn about **CTEs (Common Table Expressions)** - an even better way to organize multi-step queries! 🚀

Think of subqueries as your SQL toolkit - each type is a different tool for different jobs! 🔧🎯
