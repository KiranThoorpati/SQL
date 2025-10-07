# Standalone CTE - The Independent Helper
*A Complete Beginner's Guide*

## What is a Standalone CTE?

A **Standalone CTE** is like a recipe step that doesn't need any other steps before it. It works completely on its own!

Think of it like this:
- **Standalone CTE** = A friend who can do their homework alone without help
- **Main Query** = Another friend who needs to see the first friend's work to complete their own

### The Key Rule:
✅ **CTE is independent** - Can run by itself  
❌ **Main Query is dependent** - Needs the CTE's result to work

---

## How It Works (The Flow)

```
Database Tables → CTE Query → Intermediate Results → Main Query → Final Results
```

**Step by Step:**
1. CTE queries the database tables directly
2. CTE produces intermediate (temporary) results
3. Main query uses those intermediate results
4. We get the final output!

---

## CTE Syntax (The Recipe)

```sql
-- The Pattern:
WITH CTE_Name AS (
    -- Your query here
    SELECT columns
    FROM table
    WHERE conditions
)
-- Main query uses the CTE
SELECT * FROM CTE_Name;
```

**Important Parts:**
- `WITH` = Keyword to start a CTE
- `CTE_Name` = Name you give to your helper (like a table name)
- `AS` = Keyword meaning "is defined as"
- `( )` = Parentheses wrap the CTE definition
- Main query MUST use the exact same `CTE_Name`

---

## Real Example: School Sales Project

Let's say we have two tables:

### Table 1: Sales_Orders
```
order_id | customer_id | sale_amount
---------|-------------|------------
1        | 1           | 100
2        | 1           | 150
3        | 2           | 200
4        | 3           | 75
5        | 3           | 125
6        | 4           | 300
```

### Table 2: Customers
```
customer_id | first_name | last_name
------------|------------|----------
1           | Alice      | Smith
2           | Bob        | Jones
3           | Charlie    | Brown
4           | Diana      | Wilson
5           | Emma       | Davis
```

---

## Task: Find Total Sales Per Customer

### Step 1: Query Without CTE (Basic Version)

```sql
SELECT 
    customer_id,
    SUM(sale_amount) as total_sales
FROM sales_orders
GROUP BY customer_id;
```

**Output (Intermediate Result):**
```
customer_id | total_sales
------------|------------
1           | 250
2           | 200
3           | 200
4           | 300
```

---

### Step 2: Same Query Using CTE (Better Version)

```sql
-- Define the CTE
WITH CTE_TotalSales AS (
    SELECT 
        customer_id,
        SUM(sale_amount) as total_sales
    FROM sales_orders
    GROUP BY customer_id
)

-- Main query: Get customer names with their total sales
SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    cts.total_sales
FROM customers c
LEFT JOIN CTE_TotalSales cts ON c.customer_id = cts.customer_id
ORDER BY c.customer_id;
```

**Final Output:**
```
customer_id | first_name | last_name | total_sales
------------|------------|-----------|------------
1           | Alice      | Smith     | 250
2           | Bob        | Jones     | 200
3           | Charlie    | Brown     | 200
4           | Diana      | Wilson    | 300
5           | Emma       | Davis     | NULL
```

**Notice:** Emma has `NULL` because she didn't make any purchases (no records in sales_orders)!

---

## How to See the CTE Results Separately

Want to see what the CTE calculated? Just run the CTE query alone:

```sql
-- Run ONLY this part (without WITH and parentheses)
SELECT 
    customer_id,
    SUM(sale_amount) as total_sales
FROM sales_orders
GROUP BY customer_id;
```

This shows you the **intermediate results** before the main query uses them!

---

## Another Example: Top Students Report

### Our Table: Student_Scores
```
student_id | name    | subject | score
-----------|---------|---------|------
1          | John    | Math    | 85
1          | John    | Science | 90
2          | Sara    | Math    | 95
2          | Sara    | Science | 88
3          | Mike    | Math    | 78
3          | Mike    | Science | 82
```

### Query: Find Students' Average Scores Above 85

```sql
-- CTE calculates average for each student
WITH StudentAverages AS (
    SELECT 
        student_id,
        name,
        AVG(score) as average_score
    FROM student_scores
    GROUP BY student_id, name
)

-- Main query filters those above 85
SELECT 
    name,
    ROUND(average_score, 2) as avg_score
FROM StudentAverages
WHERE average_score > 85
ORDER BY average_score DESC;
```

**Expected Output:**
```
name | avg_score
-----|----------
Sara | 91.50
John | 87.50
```

---

## What Can You Use in a CTE?

### ✅ Allowed (You CAN use):
- `SELECT` statements
- `FROM` clause
- `WHERE` conditions
- `JOIN` operations
- `GROUP BY` for aggregations
- `HAVING` for filtering groups
- Window functions
- All aggregate functions (SUM, AVG, COUNT, etc.)

### ❌ NOT Allowed (You CANNOT use):
- `ORDER BY` clause ⚠️ **IMPORTANT!**

---

## The ORDER BY Rule

```sql
-- ❌ WRONG - This will give an ERROR!
WITH BadCTE AS (
    SELECT customer_id, SUM(sale_amount) as total
    FROM sales_orders
    GROUP BY customer_id
    ORDER BY customer_id  -- ERROR! Cannot use ORDER BY here
)
SELECT * FROM BadCTE;
```

```sql
-- ✅ CORRECT - ORDER BY in main query only!
WITH GoodCTE AS (
    SELECT customer_id, SUM(sale_amount) as total
    FROM sales_orders
    GROUP BY customer_id
)
SELECT * FROM GoodCTE
ORDER BY customer_id;  -- This works!
```

**Why?** The CTE creates temporary results - sorting happens in the final step (main query)!

---

## Treating CTE Like a Real Table

Once you create a CTE, use it just like a normal table:

```sql
WITH MyHelper AS (
    SELECT * FROM products WHERE price > 100
)

-- You can JOIN it
SELECT * FROM customers c
JOIN MyHelper m ON c.product_id = m.id;

-- You can filter it
SELECT * FROM MyHelper WHERE category = 'Electronics';

-- You can do anything you'd do with a normal table!
```

---

## Key Takeaways

📌 **Standalone CTE** = Independent, doesn't need other CTEs  
📌 **Main Query** = Depends on the CTE  
📌 **CTE lives in memory** = Fast access, temporary storage  
📌 **Treat CTE like a table** = Use it in JOINs, WHERE, etc.  
📌 **NO ORDER BY in CTE** = Only in main query  
📌 **Use same name** = CTE name in WITH must match usage in main query  

---

## Practice Problem

**Try this yourself!**

Given a table `book_loans`:
```
student_id | book_name      | days_borrowed
-----------|----------------|---------------
1          | Harry Potter   | 14
1          | Math Guide     | 7
2          | Space Atlas    | 21
3          | Harry Potter   | 10
```

**Challenge:** Write a CTE to find each student's total borrowed days, then show only students who borrowed more than 15 days total.

**Hint:** Use `SUM()` in the CTE and `WHERE` in the main query!
