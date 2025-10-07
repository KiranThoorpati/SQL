# SQL Subquery in WHERE Clause - Simple Guide

## What is a Subquery in WHERE?

A subquery in the WHERE clause helps you **filter data dynamically** instead of using fixed values. It's like asking "show me students taller than the average height" - the average changes, so you calculate it first!

**Think of it like:** Instead of saying "show scores above 75", you say "show scores above the class average" - the filter value comes from a calculation!

---

## Two Types of Operators in WHERE

### 1. Comparison Operators (Compare Values)
- `=` (equal to)
- `!=` or `<>` (not equal to)
- `>` (greater than)
- `<` (less than)
- `>=` (greater than or equal to)
- `<=` (less than or equal to)

### 2. Logical Operators (We'll learn these next!)
- `IN`, `ANY`, `ALL`, `EXISTS`

---

## Basic Syntax with Comparison Operators

```sql
SELECT columns
FROM table1
WHERE column operator (
    -- Subquery: Must return ONE value!
    SELECT calculation
    FROM table2
    WHERE condition
);
```

**IMPORTANT RULE:** ⚠️
When using comparison operators, the subquery **MUST return only ONE value** (scalar subquery)!

---

## Example 1: Products Above Average Price

**Task:** Find all products that cost more than the average price.

### Step 1: Get All Products

```sql
SELECT 
    product_id,
    product_name,
    price
FROM products;
```

**Output:**
```
product_id | product_name | price
-----------|--------------|-------
1          | Laptop       | 800
2          | Mouse        | 25
3          | Keyboard     | 75
4          | Monitor      | 300
5          | Webcam       | 150
```

### Step 2: Calculate Average Price (Test Subquery)

```sql
SELECT AVG(price) AS average_price
FROM products;
```

**Output:**
```
average_price
-------------
270
```

### Step 3: Complete Query with Subquery in WHERE ✨

```sql
SELECT 
    product_id,
    product_name,
    price,
    (SELECT AVG(price) FROM products) AS average_price
FROM products
WHERE price > (
    -- Subquery: Calculate average
    SELECT AVG(price) FROM products
);
```

**Expected Final Output:**
```
product_id | product_name | price | average_price
-----------|--------------|-------|---------------
1          | Laptop       | 800   | 270
4          | Monitor      | 300   | 270
```

---

## Example 2: Students Above Class Average

**Task:** Find students who scored above the class average.

```sql
SELECT 
    student_name,
    score,
    (SELECT AVG(score) FROM students) AS class_average
FROM students
WHERE score > (
    SELECT AVG(score) FROM students
);
```

**Expected Output:**
```
student_name | score | class_average
-------------|-------|---------------
Alice        | 95    | 78.5
Charlie      | 88    | 78.5
Emma         | 82    | 78.5
```

---

## Example 3: Find the Most Expensive Product

**Task:** Show which product has the highest price.

```sql
SELECT 
    product_name,
    price
FROM products
WHERE price = (
    SELECT MAX(price) FROM products
);
```

**Expected Output:**
```
product_name | price
-------------|-------
Laptop       | 800
```

---

## Example 4: Students in the Same Grade as Top Scorer

**Task:** Find all students in the same grade as the student with the highest score.

```sql
SELECT 
    student_name,
    grade,
    score
FROM students
WHERE grade = (
    -- Step 2: Find top scorer's grade
    SELECT grade
    FROM students
    WHERE student_id = (
        -- Step 1: Find top scorer
        SELECT student_id
        FROM students
        ORDER BY score DESC
        LIMIT 1
    )
);
```

**Expected Output:**
```
student_name | grade | score
-------------|-------|-------
Alice        | 7     | 95
Bob          | 7     | 88
Charlie      | 7     | 72
Diana        | 7     | 85
```

---

## Example 5: Books More Popular Than Average

**Task:** Find books borrowed more times than the average.

```sql
SELECT 
    book_title,
    times_borrowed
FROM (
    SELECT 
        book_title,
        COUNT(*) AS times_borrowed
    FROM library_transactions
    GROUP BY book_title
) AS book_stats
WHERE times_borrowed > (
    SELECT AVG(borrow_count)
    FROM (
        SELECT COUNT(*) AS borrow_count
        FROM library_transactions
        GROUP BY book_id
    ) AS counts
);
```

**Expected Output:**
```
book_title              | times_borrowed
------------------------|----------------
Harry Potter            | 45
Diary of a Wimpy Kid    | 52
The Hobbit             | 38
```

---

## Example 6: Employees Earning Above Department Average

**Task:** Find employees who earn more than their department's average salary.

```sql
SELECT 
    employee_name,
    department,
    salary
FROM employees e1
WHERE salary > (
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.department = e1.department
);
```

**Expected Output:**
```
employee_name | department | salary
--------------|------------|--------
John Smith    | Sales      | 65000
Mary Jones    | Marketing  | 58000
Bob Wilson    | Sales      | 70000
```

---

## Using Different Comparison Operators

### Greater Than (>)

```sql
-- Students scoring better than Bob
SELECT student_name, score
FROM students
WHERE score > (
    SELECT score FROM students WHERE student_name = 'Bob'
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

### Less Than (<)

```sql
-- Products cheaper than average
SELECT product_name, price
FROM products
WHERE price < (
    SELECT AVG(price) FROM products
);
```

**Expected Output:**
```
product_name | price
-------------|-------
Mouse        | 25
Keyboard     | 75
Webcam       | 150
```

### Equal To (=)

```sql
-- Find youngest student(s)
SELECT student_name, age
FROM students
WHERE age = (
    SELECT MIN(age) FROM students
);
```

**Expected Output:**
```
student_name | age
-------------|-----
Emma         | 12
Frank        | 12
```

### Not Equal To (!=)

```sql
-- Products not at maximum price
SELECT product_name, price
FROM products
WHERE price != (
    SELECT MAX(price) FROM products
);
```

**Expected Output:**
```
product_name | price
-------------|-------
Mouse        | 25
Keyboard     | 75
Monitor      | 300
Webcam       | 150
```

---

## How SQL Executes This Query (Behind the Scenes)

### Example Query:

```sql
SELECT product_name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

### Execution Steps:

**Step 1: Identify Subquery**
```
SQL finds: (SELECT AVG(price) FROM products)
```

**Step 2: Execute Subquery First**
```sql
SELECT AVG(price) FROM products;
```
Result: `270` (stored in memory)

**Step 3: Replace Subquery with Value**
```sql
-- SQL transforms query to:
SELECT product_name, price
FROM products
WHERE price > 270;  -- Subquery replaced!
```

**Step 4: Execute Main Query**
```
Filter products table → Keep only rows where price > 270
```

**Step 5: Show Final Results**
```
product_name | price
-------------|-------
Laptop       | 800
Monitor      | 300
```

---

## Common Mistakes to Avoid ⚠️

### Mistake 1: Subquery Returns Multiple Values

```sql
-- ❌ WRONG - Returns multiple values!
SELECT student_name
FROM students
WHERE score > (
    SELECT score FROM students  -- Returns ALL scores!
);
-- ERROR: Subquery returned more than one value!

-- ✅ CORRECT - Returns ONE value!
SELECT student_name
FROM students
WHERE score > (
    SELECT AVG(score) FROM students  -- Returns ONE value
);
```

### Mistake 2: Forgetting Parentheses

```sql
-- ❌ WRONG - No parentheses
SELECT * FROM students
WHERE score > SELECT AVG(score) FROM students;  -- ERROR!

-- ✅ CORRECT - Has parentheses
SELECT * FROM students
WHERE score > (SELECT AVG(score) FROM students);
```

---

## Real-World Examples

### Example 7: Recent Orders Only

**Task:** Find orders placed after the average order date.

```sql
SELECT 
    order_id,
    customer_name,
    order_date,
    total_amount
FROM orders
WHERE order_date > (
    SELECT AVG(order_date) FROM orders
)
ORDER BY order_date DESC;
```

**Expected Output:**
```
order_id | customer_name | order_date  | total_amount
---------|---------------|-------------|-------------
105      | Alice Brown   | 2024-03-15  | 450
104      | Bob Wilson    | 2024-03-10  | 320
103      | Mary Jones    | 2024-03-05  | 280
```

### Example 8: Products in Stock Above Average

**Task:** Find products with more stock than average.

```sql
SELECT 
    product_name,
    stock_quantity,
    (SELECT AVG(stock_quantity) FROM products) AS avg_stock
FROM products
WHERE stock_quantity > (
    SELECT AVG(stock_quantity) FROM products
)
ORDER BY stock_quantity DESC;
```

**Expected Output:**
```
product_name | stock_quantity | avg_stock
-------------|----------------|----------
Monitor      | 150            | 75
Laptop       | 120            | 75
Keyboard     | 95             | 75
```

---

## Testing Your Subquery 🔍

Always test the subquery separately first!

```sql
-- Run ONLY the subquery (highlight it without parentheses)
SELECT AVG(price) FROM products;
```

**Output:**
```
(No column name)
----------------
270
```

This confirms your subquery returns ONE value before using it in WHERE!

---

## Key Takeaways

✅ **Subquery in WHERE = Dynamic filtering**  
✅ **Must return ONLY ONE value with comparison operators**  
✅ **Use aggregations: AVG(), MAX(), MIN(), COUNT(), SUM()**  
✅ **Subquery executes FIRST, then filters main query**  
✅ **Always use parentheses ( ) around subquery**  
✅ **Test subquery separately before using it**  
✅ **Can use any comparison operator: =, >, <, >=, <=, !=**

Think of it like a two-step filter: First calculate the benchmark (subquery), then find everyone above/below it (main query)! 🎯📊
