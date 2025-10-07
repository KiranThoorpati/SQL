# SQL Common Table Expressions (CTE) - Simple Guide

## What is a CTE?

A **CTE (Common Table Expression)** is a **temporary named table** that lives inside your query. It's like creating a nickname for a complex calculation that you can use multiple times!

**Think of it like:** Creating a cheat sheet during an exam that you can refer to multiple times - but it disappears after the exam ends!

---

## CTE vs Subquery - The Big Difference

### Subquery (We Already Learned)
```sql
-- Can use result ONLY ONCE
SELECT *
FROM (
    SELECT customer_id, SUM(amount) AS total
    FROM orders
    GROUP BY customer_id
) AS subquery_result
WHERE total > 1000;
```

❌ **Problem:** Can't reuse `subquery_result` elsewhere in the query!

### CTE (New and Better!)
```sql
-- Can use result MULTIPLE TIMES!
WITH sales_summary AS (
    SELECT customer_id, SUM(amount) AS total
    FROM orders
    GROUP BY customer_id
)
SELECT * FROM sales_summary WHERE total > 1000
UNION
SELECT * FROM sales_summary WHERE total < 500;
```

✅ **Benefit:** Used `sales_summary` TWICE in the same query!

---

## Basic CTE Syntax

```sql
WITH cte_name AS (
    -- Your query here
    SELECT columns
    FROM table
    WHERE conditions
)
-- Main query uses the CTE
SELECT *
FROM cte_name;
```

**Key Parts:**
1. **WITH** = Start the CTE
2. **cte_name** = Give it a name (like a variable)
3. **AS ( ... )** = Define the query
4. **Main query** = Use the CTE name like a table

---

## Example 1: Simple CTE - Customer Totals

**Task:** Calculate total spending per customer, then show only big spenders.

```sql
-- CTE: Calculate totals
WITH customer_totals AS (
    SELECT 
        customer_id,
        customer_name,
        SUM(order_amount) AS total_spent
    FROM orders
    JOIN customers ON orders.customer_id = customers.customer_id
    GROUP BY customer_id, customer_name
)
-- Main Query: Use the CTE
SELECT 
    customer_name,
    total_spent
FROM customer_totals
WHERE total_spent > 1000
ORDER BY total_spent DESC;
```

**Expected Output:**
```
customer_name | total_spent
--------------|------------
Bob Wilson    | 3800
John Smith    | 2500
Alice Brown   | 1800
Mary Jones    | 1200
```

---

## Example 2: Using CTE Multiple Times (The Power!)

**Task:** Show both high spenders AND low spenders from the same calculation.

```sql
-- CTE: Calculate once
WITH customer_spending AS (
    SELECT 
        customer_id,
        customer_name,
        SUM(order_amount) AS total_spent
    FROM orders
    JOIN customers ON orders.customer_id = customers.customer_id
    GROUP BY customer_id, customer_name
)
-- Main Query: Use CTE TWICE!
SELECT 'High Spender' AS category, customer_name, total_spent
FROM customer_spending
WHERE total_spent > 2000

UNION ALL

SELECT 'Low Spender' AS category, customer_name, total_spent
FROM customer_spending
WHERE total_spent < 1000;
```

**Expected Output:**
```
category      | customer_name | total_spent
--------------|---------------|------------
High Spender  | Bob Wilson    | 3800
High Spender  | John Smith    | 2500
Low Spender   | Emma Davis    | 450
Low Spender   | Tom Brown     | 650
```

**Magic!** We used `customer_spending` TWICE without recalculating!

---

## Example 3: Student Grade Analysis

**Task:** Find students above and below their grade average.

```sql
-- CTE: Calculate grade averages
WITH grade_averages AS (
    SELECT 
        grade,
        AVG(score) AS avg_score
    FROM students
    GROUP BY grade
)
-- Main Query: Join CTE with students
SELECT 
    s.student_name,
    s.grade,
    s.score,
    ga.avg_score,
    CASE 
        WHEN s.score > ga.avg_score THEN 'Above Average'
        ELSE 'Below Average'
    END AS performance
FROM students s
JOIN grade_averages ga ON s.grade = ga.grade
ORDER BY s.grade, s.score DESC;
```

**Expected Output:**
```
student_name | grade | score | avg_score | performance
-------------|-------|-------|-----------|---------------
Alice        | 7     | 95    | 80        | Above Average
Charlie      | 7     | 88    | 80        | Above Average
Bob          | 7     | 72    | 80        | Below Average
Emma         | 8     | 90    | 82        | Above Average
Diana        | 8     | 75    | 82        | Below Average
```

---

## Example 4: Multiple CTEs (Advanced!)

You can create MORE THAN ONE CTE!

```sql
-- CTE 1: Calculate order totals
WITH order_totals AS (
    SELECT 
        customer_id,
        COUNT(*) AS order_count,
        SUM(order_amount) AS total_spent
    FROM orders
    GROUP BY customer_id
),
-- CTE 2: Classify customers
customer_categories AS (
    SELECT 
        customer_id,
        order_count,
        total_spent,
        CASE 
            WHEN total_spent > 2000 THEN 'VIP'
            WHEN total_spent > 1000 THEN 'Premium'
            ELSE 'Regular'
        END AS category
    FROM order_totals
)
-- Main Query: Use BOTH CTEs
SELECT 
    c.customer_name,
    cc.category,
    cc.order_count,
    cc.total_spent
FROM customer_categories cc
JOIN customers c ON cc.customer_id = c.customer_id
ORDER BY cc.total_spent DESC;
```

**Expected Output:**
```
customer_name | category | order_count | total_spent
--------------|----------|-------------|------------
Bob Wilson    | VIP      | 8           | 3800
John Smith    | VIP      | 5           | 2500
Alice Brown   | Premium  | 4           | 1800
Mary Jones    | Premium  | 3           | 1200
Tom Brown     | Regular  | 2           | 650
```

---

## How CTE Works (Behind the Scenes)

### Process:

```
Step 1: Execute CTE query
   ↓
Step 2: Store result in memory as temporary table
   ↓
Step 3: Main query can use CTE like a real table
   ↓
Step 4: Query finishes → CTE disappears forever!
```

### Visual Example:

```sql
WITH top_students AS (
    SELECT * FROM students WHERE score >= 80
)
SELECT student_name, score FROM top_students;
```

**What Happens:**
1. **CTE runs:** Creates temporary `top_students` table
2. **Main query:** Uses `top_students` like a normal table
3. **Query ends:** `top_students` deleted from memory

---

## CTE vs Subquery Comparison

| Feature | Subquery | CTE |
|---------|----------|-----|
| **Writing Direction** | Bottom to Top | Top to Bottom |
| **Reusability** | Use ONCE only | Use MULTIPLE times |
| **Readability** | Harder to read | Easier to read |
| **Naming** | Optional alias | Must have name |
| **Multiple Uses** | Must rewrite | Use same name |

### Subquery Example (Bottom to Top)

```sql
-- Read from INSIDE OUT (confusing!)
SELECT *
FROM (
    SELECT customer_id, SUM(amount) AS total
    FROM orders
    GROUP BY customer_id
) AS totals
WHERE total > 1000;
```

### CTE Example (Top to Bottom)

```sql
-- Read from TOP to BOTTOM (clear!)
WITH totals AS (
    SELECT customer_id, SUM(amount) AS total
    FROM orders
    GROUP BY customer_id
)
SELECT * FROM totals WHERE total > 1000;
```

---

## Real-World Example: Library System

**Task:** Find popular books and show who borrowed them.

```sql
-- CTE 1: Find popular books
WITH popular_books AS (
    SELECT 
        book_id,
        book_title,
        COUNT(*) AS times_borrowed
    FROM library_transactions
    GROUP BY book_id, book_title
    HAVING COUNT(*) > 5
),
-- CTE 2: Get current borrowers
current_borrowers AS (
    SELECT 
        book_id,
        student_name,
        borrow_date
    FROM library_transactions
    JOIN students ON library_transactions.student_id = students.student_id
    WHERE return_date IS NULL
)
-- Main Query: Combine both CTEs
SELECT 
    pb.book_title,
    pb.times_borrowed,
    cb.student_name AS current_borrower,
    cb.borrow_date
FROM popular_books pb
LEFT JOIN current_borrowers cb ON pb.book_id = cb.book_id
ORDER BY pb.times_borrowed DESC;
```

**Expected Output:**
```
book_title           | times_borrowed | current_borrower | borrow_date
---------------------|----------------|------------------|------------
Harry Potter         | 45             | Alice            | 2024-03-10
Diary of a Wimpy Kid | 38             | Charlie          | 2024-03-12
The Hobbit          | 32             | NULL             | NULL
Percy Jackson        | 28             | Emma             | 2024-03-15
```

---

## Important CTE Rules

### ✅ DO:
- Give CTE a clear, descriptive name
- Use for complex calculations you need multiple times
- Chain multiple CTEs together
- Think of CTE as a temporary table

### ❌ DON'T:
- Try to use CTE in a different query (it disappears!)
- Forget the WITH keyword
- Rewrite the same calculation multiple times (use CTE!)

---

## Key Takeaways

✅ **CTE = Temporary named result set**  
✅ **Reusable multiple times** in same query  
✅ **Write top to bottom** (easier to read!)  
✅ **Disappears after query ends**  
✅ **Can create multiple CTEs** in one query  
✅ **Better than subqueries** for complex queries  
✅ **Think of it as a virtual table**  

**When to Use CTE:**
- Need to use same calculation multiple times
- Want cleaner, more readable code
- Building complex multi-step queries
- Breaking down complicated logic

Think of CTEs like sticky notes during homework - you can refer to them as many times as needed, but they disappear when you're done! 📝✨
