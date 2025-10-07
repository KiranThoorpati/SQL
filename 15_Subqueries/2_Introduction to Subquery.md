# SQL Subqueries - Simple Guide

## What is a Subquery?

A **subquery** is like a helper query that lives inside another query. Think of it like a math problem where you solve the inner parentheses first!

**Example:** (5 + (3 × 2)) = (5 + 6) = 11

In SQL, the inner query (subquery) runs first, and its result helps the outer query (main query).

## How It Works - Step by Step

### The Old Way (What We Already Know):

```
Database Tables → SQL Query → Results We See
```

### The New Way (With Subqueries):

```
Database Tables → Subquery → Intermediate Results → Main Query → Final Results We See
```

## Visual Example

**Step 1:** Subquery runs first (we don't see this result)
```sql
SELECT customer_id FROM orders WHERE total > 1000
```
Result: [1, 3, 5] ← Hidden intermediate result

**Step 2:** Main query uses the subquery result
```sql
SELECT customer_name 
FROM customers 
WHERE customer_id IN (subquery result)
```

## Real SQL Examples

### Example 1: Find Customers Who Made Big Orders

```sql
-- Main Query with Subquery inside
SELECT customer_name, city
FROM customers
WHERE customer_id IN (
    -- Subquery: Find customers with orders over $1000
    SELECT customer_id 
    FROM orders 
    WHERE total_amount > 1000
);
```

**What Happens Behind the Scenes:**

**Step 1 - Subquery runs first:**
```
customer_id
-----------
1
3
5
```
(We don't see this - it's hidden!)

**Step 2 - Main query uses those IDs:**

**Expected Final Output:**
```
customer_name | city
--------------|-------------
John Smith    | New York
Mary Johnson  | Chicago
Bob Wilson    | Los Angeles
```

### Example 2: Find Products More Expensive Than Average

```sql
SELECT product_name, price
FROM products
WHERE price > (
    -- Subquery: Calculate average price
    SELECT AVG(price) 
    FROM products
);
```

**What Happens:**

**Step 1 - Subquery calculates average:**
```
avg_price
---------
150
```
(Hidden intermediate result)

**Step 2 - Main query finds products above that average:**

**Expected Output:**
```
product_name | price
-------------|-------
Laptop       | 800
Tablet       | 400
Smartphone   | 600
```

### Example 3: Students Who Scored Above Class Average

```sql
SELECT student_name, score
FROM test_scores
WHERE score > (
    -- Subquery: Find average score
    SELECT AVG(score) 
    FROM test_scores
);
```

**Behind the Scenes:**

**Step 1 - Subquery:**
```
Average score = 75
```

**Step 2 - Final Output:**
```
student_name | score
-------------|-------
Alice        | 95
Charlie      | 88
Emma         | 82
```

### Example 4: Find Employees in the Same Department as John

```sql
SELECT employee_name, department
FROM employees
WHERE department = (
    -- Subquery: Find John's department
    SELECT department 
    FROM employees 
    WHERE employee_name = 'John Smith'
);
```

**Step 1 - Subquery result:**
```
department = 'Sales'
```

**Step 2 - Expected Output:**
```
employee_name | department
--------------|------------
John Smith    | Sales
Mary Jones    | Sales
Tom Brown     | Sales
```

## Important Rules About Subqueries

### Rule 1: Subquery Runs First
The inner query always executes before the outer query.

### Rule 2: Results Are Temporary
Once the main query finishes, the intermediate results **disappear forever**!

```sql
-- Query 1
SELECT * FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders);

-- Query 2 (completely separate)
SELECT * FROM products;
-- Query 2 CANNOT access the subquery results from Query 1!
```

### Rule 3: Results Are Private
Only the main query can see the subquery results. Other queries cannot access them.

## Real-World Comparison

Think of making a sandwich:

1. **Subquery:** Go to the fridge and get ingredients (hidden step)
2. **Main Query:** Make the sandwich with those ingredients (what you see)
3. **Result:** Finished sandwich! 🥪

The intermediate step (getting ingredients) happens, but you only see the final sandwich!

## Practice Examples

### Example 5: Find Cheapest Product Category

```sql
SELECT product_name, category, price
FROM products
WHERE category = (
    -- Find category with lowest average price
    SELECT category
    FROM products
    GROUP BY category
    ORDER BY AVG(price)
    LIMIT 1
);
```

**Expected Output:**
```
product_name | category | price
-------------|----------|-------
Pencil       | Stationery| 2
Eraser       | Stationery| 1
Notebook     | Stationery| 5
```

### Example 6: Compare Each Student to Top Score

```sql
SELECT 
    student_name,
    score,
    (SELECT MAX(score) FROM test_scores) AS highest_score,
    (SELECT MAX(score) FROM test_scores) - score AS points_behind
FROM test_scores;
```

**Expected Output:**
```
student_name | score | highest_score | points_behind
-------------|-------|---------------|---------------
Alice        | 95    | 95            | 0
Bob          | 88    | 95            | 7
Charlie      | 75    | 95            | 20
Diana        | 92    | 95            | 3
```

## Key Takeaways

✅ Subquery = A query inside another query  
✅ Subquery runs **first**, main query runs **second**  
✅ Intermediate results are **temporary** and **private**  
✅ Subqueries help solve complex problems in simple steps  
✅ Once the query finishes, intermediate results are **destroyed**

Think of subqueries as your helper that does some work first, gives you the answer privately, and then disappears!
