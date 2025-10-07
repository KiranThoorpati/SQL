# SQL Subquery in SELECT Clause - Simple Guide

## What is a Subquery in SELECT?

A subquery in the SELECT clause lets you add **calculated values** alongside your regular columns. It's like adding a special extra column that comes from a different calculation!

**Think of it like:** Adding "class average" next to each student's score - the average is calculated separately but shown with each student's data.

---

## Basic Syntax

```sql
SELECT 
    column1,
    column2,
    (
        -- Subquery: Must return ONE value only!
        SELECT calculation
        FROM another_table
    ) AS extra_column
FROM main_table;
```

**SUPER IMPORTANT RULE:** ⚠️
The subquery in SELECT clause **MUST return only ONE value** (scalar subquery)!

---

## Example 1: Show Products with Total Order Count

**Task:** Show product details along with the total number of orders in the system.

### Step 1: Get Product Details (Easy Part)

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
```

### Step 2: Get Total Orders (Separate Query)

```sql
SELECT COUNT(*) AS total_orders
FROM orders;
```

**Output:**
```
total_orders
------------
10
```

### Step 3: Combine Using Subquery in SELECT ✨

```sql
SELECT 
    product_id,
    product_name,
    price,
    (SELECT COUNT(*) FROM orders) AS total_orders
FROM products;
```

**Expected Final Output:**
```
product_id | product_name | price | total_orders
-----------|--------------|-------|-------------
1          | Laptop       | 800   | 10
2          | Mouse        | 25    | 10
3          | Keyboard     | 75    | 10
4          | Monitor      | 300   | 10
```

**Notice:** The total_orders value (10) repeats for every row!

---

## Example 2: Compare Each Student to Class Average

**Task:** Show each student's score alongside the class average.

```sql
SELECT 
    student_name,
    score,
    (SELECT AVG(score) FROM students) AS class_average,
    score - (SELECT AVG(score) FROM students) AS difference
FROM students;
```

**Expected Output:**
```
student_name | score | class_average | difference
-------------|-------|---------------|------------
Alice        | 95    | 78.5          | 16.5
Bob          | 72    | 78.5          | -6.5
Charlie      | 88    | 78.5          | 9.5
Diana        | 76    | 78.5          | -2.5
Emma         | 82    | 78.5          | 3.5
```

---

## Example 3: Products with Highest and Lowest Prices

**Task:** Show each product with the highest and lowest prices in the store.

```sql
SELECT 
    product_name,
    price,
    (SELECT MAX(price) FROM products) AS highest_price,
    (SELECT MIN(price) FROM products) AS lowest_price,
    price - (SELECT MIN(price) FROM products) AS above_minimum
FROM products;
```

**Expected Output:**
```
product_name | price | highest_price | lowest_price | above_minimum
-------------|-------|---------------|--------------|---------------
Laptop       | 800   | 800           | 25           | 775
Mouse        | 25    | 800           | 25           | 0
Keyboard     | 75    | 800           | 25           | 50
Monitor      | 300   | 800           | 25           | 275
Webcam       | 150   | 800           | 25           | 125
```

---

## Example 4: Library Books with Total Member Count

**Task:** Show book details with the total number of library members.

```sql
SELECT 
    book_id,
    book_title,
    author,
    (SELECT COUNT(*) FROM members) AS total_members,
    (SELECT COUNT(DISTINCT student_id) FROM borrowed_books) AS active_borrowers
FROM books;
```

**Expected Output:**
```
book_id | book_title           | author        | total_members | active_borrowers
--------|---------------------|---------------|---------------|------------------
1       | Harry Potter        | J.K. Rowling  | 150           | 45
2       | The Hobbit          | J.R.R. Tolkien| 150           | 45
3       | Charlotte's Web     | E.B. White    | 150           | 45
4       | Diary of a Wimpy Kid| Jeff Kinney   | 150           | 45
```

---

## Common Mistake: Returning Multiple Values ❌

### Wrong Example:

```sql
-- ❌ ERROR: Returns multiple values!
SELECT 
    student_name,
    score,
    (SELECT score FROM students) AS all_scores  -- Returns many values!
FROM students;
```

**Error Message:** "Subquery returned more than one value!"

### Correct Example:

```sql
-- ✅ CORRECT: Returns ONE value!
SELECT 
    student_name,
    score,
    (SELECT AVG(score) FROM students) AS average_score  -- Returns ONE value!
FROM students;
```

---

## How SQL Executes This Query (Behind the Scenes)

### Example Query:

```sql
SELECT 
    product_id,
    product_name,
    price,
    (SELECT COUNT(*) FROM orders) AS total_orders
FROM products;
```

### Execution Steps:

**Step 1: Find and Execute Subquery**
```sql
SELECT COUNT(*) FROM orders;
```
Result: `10` (stored in memory)

**Step 2: Replace Subquery with Result**
```sql
-- SQL transforms the query to:
SELECT 
    product_id,
    product_name,
    price,
    10 AS total_orders  -- Subquery replaced with value!
FROM products;
```

**Step 3: Execute Main Query**
```
Query products table → Get all rows → Add "10" to each row
```

**Step 4: Show Final Result**
```
product_id | product_name | price | total_orders
-----------|--------------|-------|-------------
1          | Laptop       | 800   | 10
2          | Mouse        | 25    | 10
...
```

---

## Real-World Examples

### Example 5: Sales Report with Company Total

```sql
SELECT 
    employee_name,
    department,
    sales_amount,
    (SELECT SUM(sales_amount) FROM sales) AS company_total,
    ROUND(sales_amount * 100.0 / (SELECT SUM(sales_amount) FROM sales), 2) AS percentage
FROM sales;
```

**Expected Output:**
```
employee_name | department | sales_amount | company_total | percentage
--------------|------------|--------------|---------------|------------
John Smith    | Sales      | 50000        | 500000        | 10.00
Mary Jones    | Sales      | 75000        | 500000        | 15.00
Bob Wilson    | Marketing  | 45000        | 500000        | 9.00
Alice Brown   | Sales      | 60000        | 500000        | 12.00
```

### Example 6: Test Scores with Statistics

```sql
SELECT 
    student_name,
    score,
    (SELECT AVG(score) FROM test_scores) AS average,
    (SELECT MAX(score) FROM test_scores) AS highest,
    (SELECT MIN(score) FROM test_scores) AS lowest,
    CASE 
        WHEN score >= (SELECT AVG(score) FROM test_scores) THEN 'Above Average'
        ELSE 'Below Average'
    END AS performance
FROM test_scores;
```

**Expected Output:**
```
student_name | score | average | highest | lowest | performance
-------------|-------|---------|---------|--------|---------------
Alice        | 95    | 78      | 95      | 55     | Above Average
Bob          | 72    | 78      | 95      | 55     | Below Average
Charlie      | 88    | 78      | 95      | 55     | Above Average
Diana        | 65    | 78      | 95      | 55     | Below Average
Emma         | 55    | 78      | 95      | 55     | Below Average
```

---

## Testing Your Subquery 🔍

Want to see what the subquery returns? Run it separately!

```sql
-- Highlight and run ONLY this part (without parentheses)
SELECT COUNT(*) FROM orders;
```

**Output:**
```
(No column name)
----------------
10
```

This helps you verify the subquery works before using it in SELECT!

---

## When to Use Subquery in SELECT

✅ **Use when:**
- You need to show aggregate statistics with detail rows
- You want to compare each row to a total/average
- You need the same calculated value for every row
- The calculation comes from a different table

❌ **Don't use when:**
- The subquery returns multiple values (use JOIN or window functions instead)
- You need different values for each row (use correlated subquery or window functions)

---

## Key Takeaways

✅ **Subquery in SELECT = Add extra calculated column**  
✅ **Must return ONLY ONE value (scalar subquery)**  
✅ **Use aggregations: AVG(), MAX(), MIN(), COUNT(), SUM()**  
✅ **Same value repeats for all rows**  
✅ **Subquery executes FIRST, then main query**  
✅ **Test subquery separately by running it alone**  
✅ **Use parentheses ( ) around the subquery**

Think of it like adding a sticky note to every page of your notebook - the same information appears on every page! 📝✨
