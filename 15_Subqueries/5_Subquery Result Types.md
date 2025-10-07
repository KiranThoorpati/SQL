# SQL Subquery Result Types - Simple Guide

## The Three Types Based on What They Return

Think of subqueries like vending machines - some give you one item, some give you a column of items, and some give you a whole tray!

---

## Type 1: Scalar Subquery (Returns ONE Single Value) 1️⃣

**What it returns:** Just **one value** - one number, one word, one date.

**Think of it like:** A calculator giving you one answer!

### How to Create a Scalar Subquery

Usually by using **aggregation functions**: AVG(), SUM(), COUNT(), MAX(), MIN()

### Example 1: Find the Average Score

```sql
-- This returns only ONE value
SELECT AVG(score) AS average_score
FROM students;
```

**Expected Output:**
```
average_score
-------------
78.5
```
☝️ One row, one column = **Scalar**!

### Example 2: Using Scalar Subquery in WHERE

```sql
-- Find students who scored above average
SELECT student_name, score
FROM students
WHERE score > (
    SELECT AVG(score) FROM students  -- Returns one value: 78.5
);
```

**Expected Output:**
```
student_name | score
-------------|-------
Alice        | 95
Charlie      | 88
Emma         | 82
```

### Example 3: Scalar Subquery in SELECT

```sql
-- Show each product with the average price
SELECT 
    product_name,
    price,
    (SELECT AVG(price) FROM products) AS avg_price
FROM products;
```

**Expected Output:**
```
product_name | price | avg_price
-------------|-------|----------
Laptop       | 800   | 275
Mouse        | 25    | 275
Keyboard     | 75    | 275
Monitor      | 300   | 275
```

### Example 4: Compare to Maximum Value

```sql
-- Find the highest salary
SELECT MAX(salary) AS highest_salary
FROM employees;
```

**Expected Output:**
```
highest_salary
--------------
85000
```

---

## Type 2: Row Subquery (Returns Multiple Rows, ONE Column) ⬇️

**What it returns:** A **list** of values in one column (like a shopping list!)

**Think of it like:** A column of student names or a list of numbers!

### Example 1: Get List of Customer IDs

```sql
-- Returns one column with many rows
SELECT customer_id
FROM orders;
```

**Expected Output:**
```
customer_id
-----------
1
3
1
5
2
3
1
```
☝️ Multiple rows, one column = **Row Subquery**!

### Example 2: Using Row Subquery with IN

```sql
-- Find customers who placed orders
SELECT customer_name
FROM customers
WHERE customer_id IN (
    -- Row subquery: list of customer IDs
    SELECT customer_id 
    FROM orders
);
```

**Expected Output:**
```
customer_name
--------------
John Smith
Mary Johnson
Bob Wilson
```

### Example 3: Find Active Students

```sql
-- Students who borrowed books
SELECT student_name
FROM students
WHERE student_id IN (
    SELECT student_id FROM library_books
);
```

**Expected Output:**
```
student_name
-------------
Alice
Bob
Charlie
Diana
Emma
```

### Example 4: Products in Recent Orders

```sql
-- Find which products were ordered this month
SELECT product_name
FROM products
WHERE product_id IN (
    SELECT product_id 
    FROM orders 
    WHERE MONTH(order_date) = MONTH(CURRENT_DATE)
);
```

**Expected Output:**
```
product_name
-------------
Laptop
Mouse
Keyboard
Webcam
```

---

## Type 3: Table Subquery (Returns Multiple Rows AND Multiple Columns) 📊

**What it returns:** A complete **mini-table** with rows and columns!

**Think of it like:** A full spreadsheet or report card!

### Example 1: Select Multiple Columns

```sql
-- Returns multiple columns and rows
SELECT order_id, customer_id, order_date
FROM orders;
```

**Expected Output:**
```
order_id | customer_id | order_date
---------|-------------|------------
101      | 1           | 2024-01-15
102      | 3           | 2024-01-16
103      | 1           | 2024-01-18
104      | 5           | 2024-01-20
105      | 2           | 2024-01-22
```
☝️ Multiple rows AND multiple columns = **Table Subquery**!

### Example 2: Using Table Subquery in FROM

```sql
-- Use a table subquery as a temporary table
SELECT 
    grade,
    AVG(score) AS grade_average
FROM (
    -- Table subquery with multiple columns
    SELECT student_name, grade, score
    FROM students
    WHERE score >= 70
) AS passing_students
GROUP BY grade;
```

**Expected Output:**
```
grade | grade_average
------|---------------
6     | 82.5
7     | 88.3
8     | 85.7
```

### Example 3: Filter and Calculate

```sql
-- Create a summary from filtered data
SELECT *
FROM (
    SELECT 
        customer_id,
        customer_name,
        city,
        registration_date
    FROM customers
    WHERE city IN ('New York', 'Chicago', 'Boston')
) AS east_coast_customers;
```

**Expected Output:**
```
customer_id | customer_name | city      | registration_date
------------|---------------|-----------|------------------
1           | John Smith    | New York  | 2023-05-10
3           | Mary Johnson  | Chicago   | 2023-06-15
5           | Bob Wilson    | Boston    | 2023-07-20
7           | Emma Davis    | New York  | 2023-08-12
```

### Example 4: Complex Filtering

```sql
-- Students with complete information who passed
SELECT *
FROM (
    SELECT 
        student_id,
        student_name,
        grade,
        score,
        attendance_rate
    FROM students
    WHERE score >= 60 AND attendance_rate >= 75
) AS qualified_students
WHERE grade >= 7;
```

**Expected Output:**
```
student_id | student_name | grade | score | attendance_rate
-----------|--------------|-------|-------|----------------
5          | Alice        | 7     | 95    | 98
8          | Charlie      | 7     | 88    | 92
12         | Emma         | 8     | 90    | 95
```

---

## Visual Summary

### Scalar Subquery
```
┌─────┐
│  38 │  ← ONE value
└─────┘
```

### Row Subquery
```
┌─────┐
│  1  │
│  3  │  ← ONE column
│  1  │     Multiple rows
│  5  │
└─────┘
```

### Table Subquery
```
┌────┬─────┬──────┐
│ 101│  1  │ Jan  │
│ 102│  3  │ Jan  │  ← Multiple columns
│ 103│  1  │ Feb  │     Multiple rows
│ 104│  5  │ Feb  │
└────┴─────┴──────┘
```

---

## Quick Comparison Table

| Type | Rows | Columns | Example Use | Common Functions |
|------|------|---------|-------------|------------------|
| **Scalar** | 1 | 1 | Compare to average | AVG(), MAX(), MIN(), SUM() |
| **Row** | Many | 1 | List for IN clause | SELECT one column |
| **Table** | Many | Many | Temporary table in FROM | SELECT multiple columns |

---

## Practice Examples

### Challenge 1: Identify the Type

```sql
SELECT COUNT(*) FROM students;
```
**Answer:** Scalar (returns one value)

### Challenge 2: Identify the Type

```sql
SELECT student_name FROM students WHERE grade = 7;
```
**Answer:** Row (returns multiple rows, one column)

### Challenge 3: Identify the Type

```sql
SELECT student_name, grade, score FROM students WHERE score > 80;
```
**Answer:** Table (returns multiple rows and columns)

---

## Key Takeaways

✅ **Scalar Subquery** = One single value (like a calculator result)  
✅ **Row Subquery** = A list of values (like a shopping list)  
✅ **Table Subquery** = A mini-table (like a spreadsheet)  
✅ Use aggregation functions (AVG, MAX, MIN) to create scalar subqueries  
✅ Table subqueries are most flexible - use them in FROM clause!

Understanding these types helps you choose the right subquery for your task! 🎯
