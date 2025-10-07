# Multiple Standalone CTEs - Many Independent Helpers!
*Working with Multiple CTEs Together*

## What are Multiple Standalone CTEs?

Imagine you have a big school project and you ask **4 different friends** to help you. Each friend does their own task independently, and then you combine all their work for the final project!

That's exactly what Multiple Standalone CTEs do:
- Each CTE works independently
- Each CTE queries the database directly
- All CTEs are self-contained (don't depend on each other)
- The main query combines all their results

---

## The Flow Diagram

```
                    DATABASE
                        |
        ----------------+----------------
        |       |       |       |
      CTE-1   CTE-2   CTE-3   CTE-4
        |       |       |       |
        ----------------+----------------
                        |
                   MAIN QUERY
                        |
                  FINAL RESULT
```

**What happens:**
1. Each CTE queries the database independently
2. Each CTE creates its own intermediate result
3. Main query collects data from all CTEs
4. We get the final output!

---

## Syntax Rules (Very Important!)

```sql
-- Pattern for Multiple CTEs:
WITH CTE_1 AS (
    -- First CTE definition
    SELECT ...
),  -- ← COMMA, not WITH!

CTE_2 AS (
    -- Second CTE definition
    SELECT ...
),  -- ← COMMA again!

CTE_3 AS (
    -- Third CTE definition
    SELECT ...
)  -- ← NO COMMA for last CTE!

-- Main query uses all CTEs
SELECT * 
FROM CTE_1
JOIN CTE_2 ON ...
JOIN CTE_3 ON ...;
```

**Key Rules:**
✅ Only the **FIRST** CTE uses `WITH`  
✅ All other CTEs use **COMMA** (`,`) instead of `WITH`  
✅ The **LAST** CTE has **NO COMMA** after it  
❌ Common mistakes: Using `WITH` for second CTE, or adding comma to last CTE

---

## Real Example: Student Progress Report

Let's build a report step by step using multiple CTEs!

### Our Tables:

**Table 1: Orders**
```
order_id | customer_id | order_date | sale_amount
---------|-------------|------------|------------
1        | 1           | 2024-01-15 | 100
2        | 1           | 2024-02-20 | 150
3        | 2           | 2024-01-10 | 200
4        | 3           | 2024-03-05 | 75
5        | 3           | 2024-03-15 | 125
6        | 4           | 2024-02-28 | 300
```

**Table 2: Customers**
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

## Step 1: Find Total Sales Per Customer

```sql
WITH CTE_TotalSales AS (
    SELECT 
        customer_id,
        SUM(sale_amount) as total_sales
    FROM orders
    GROUP BY customer_id
)

SELECT * FROM CTE_TotalSales;
```

**Intermediate Result 1:**
```
customer_id | total_sales
------------|------------
1           | 250
2           | 200
3           | 200
4           | 300
```

---

## Step 2: Add Last Order Date (Second CTE!)

Now let's add another CTE to find when each customer last ordered:

```sql
-- First CTE: Total Sales
WITH CTE_TotalSales AS (
    SELECT 
        customer_id,
        SUM(sale_amount) as total_sales
    FROM orders
    GROUP BY customer_id
),  -- ← COMMA here!

-- Second CTE: Last Order Date
CTE_LastOrder AS (
    SELECT 
        customer_id,
        MAX(order_date) as last_order
    FROM orders
    GROUP BY customer_id
)  -- ← NO COMMA (it's the last CTE)

-- Main Query: Combine everything
SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    cts.total_sales,
    clo.last_order
FROM customers c
LEFT JOIN CTE_TotalSales cts ON c.customer_id = cts.customer_id
LEFT JOIN CTE_LastOrder clo ON c.customer_id = clo.customer_id
ORDER BY c.customer_id;
```

**Final Output:**
```
customer_id | first_name | last_name | total_sales | last_order
------------|------------|-----------|-------------|------------
1           | Alice      | Smith     | 250         | 2024-02-20
2           | Bob        | Jones     | 200         | 2024-01-10
3           | Charlie    | Brown     | 200         | 2024-03-15
4           | Diana      | Wilson    | 300         | 2024-02-28
5           | Emma       | Davis     | NULL        | NULL
```

**Notice:**
- First 3 columns from **customers table**
- **total_sales** from **CTE_TotalSales**
- **last_order** from **CTE_LastOrder**
- Emma has NULL because she never ordered

---

## Another Example: School Library System

### Our Tables:

**Books_Borrowed:**
```
student_id | book_name      | borrow_date | return_date
-----------|----------------|-------------|------------
1          | Math Guide     | 2024-01-10  | 2024-01-20
1          | Science Fun    | 2024-02-05  | 2024-02-15
2          | Space Atlas    | 2024-01-15  | 2024-02-10
3          | Math Guide     | 2024-02-01  | 2024-02-08
```

**Students:**
```
student_id | name    | class
-----------|---------|------
1          | John    | 7A
2          | Sara    | 7B
3          | Mike    | 7A
4          | Lisa    | 7B
```

---

### Task: Create a Complete Student Reading Report

```sql
-- CTE 1: Count books borrowed per student
WITH BooksCount AS (
    SELECT 
        student_id,
        COUNT(*) as total_books
    FROM books_borrowed
    GROUP BY student_id
),

-- CTE 2: Calculate total reading days per student
ReadingDays AS (
    SELECT 
        student_id,
        SUM(JULIANDAY(return_date) - JULIANDAY(borrow_date)) as total_days
    FROM books_borrowed
    GROUP BY student_id
),

-- CTE 3: Find last book borrowed
LastBook AS (
    SELECT 
        student_id,
        book_name as last_book_borrowed
    FROM (
        SELECT 
            student_id,
            book_name,
            ROW_NUMBER() OVER (PARTITION BY student_id ORDER BY borrow_date DESC) as rn
        FROM books_borrowed
    )
    WHERE rn = 1
)

-- Main Query: Combine all information
SELECT 
    s.student_id,
    s.name,
    s.class,
    COALESCE(bc.total_books, 0) as books_borrowed,
    COALESCE(rd.total_days, 0) as total_reading_days,
    lb.last_book_borrowed
FROM students s
LEFT JOIN BooksCount bc ON s.student_id = bc.student_id
LEFT JOIN ReadingDays rd ON s.student_id = rd.student_id
LEFT JOIN LastBook lb ON s.student_id = lb.student_id
ORDER BY s.student_id;
```

**Expected Output:**
```
student_id | name | class | books_borrowed | total_reading_days | last_book_borrowed
-----------|------|-------|----------------|--------------------|-----------------
1          | John | 7A    | 2              | 20                 | Science Fun
2          | Sara | 7B    | 1              | 26                 | Space Atlas
3          | Mike | 7A    | 1              | 7                  | Math Guide
4          | Lisa | 7B    | 0              | 0                  | NULL
```

---

## Simpler Example with 3 CTEs

Let's make a Grade Report:

**Scores Table:**
```
student_id | subject | score
-----------|---------|------
1          | Math    | 85
1          | Science | 90
2          | Math    | 95
2          | Science | 80
3          | Math    | 70
3          | Science | 75
```

```sql
-- CTE 1: Average score per student
WITH StudentAvg AS (
    SELECT 
        student_id,
        AVG(score) as avg_score
    FROM scores
    GROUP BY student_id
),

-- CTE 2: Highest score per student
StudentMax AS (
    SELECT 
        student_id,
        MAX(score) as highest_score
    FROM scores
    GROUP BY student_id
),

-- CTE 3: Count subjects per student
SubjectCount AS (
    SELECT 
        student_id,
        COUNT(*) as subjects_taken
    FROM scores
    GROUP BY student_id
)

-- Main Query: Complete report
SELECT 
    sa.student_id,
    ROUND(sa.avg_score, 2) as average,
    sm.highest_score as best_score,
    sc.subjects_taken
FROM StudentAvg sa
JOIN StudentMax sm ON sa.student_id = sm.student_id
JOIN SubjectCount sc ON sa.student_id = sc.student_id
ORDER BY sa.avg_score DESC;
```

**Expected Output:**
```
student_id | average | best_score | subjects_taken
-----------|---------|------------|---------------
2          | 87.50   | 95         | 2
1          | 87.50   | 90         | 2
3          | 72.50   | 75         | 2
```

---

## Testing Individual CTEs

Want to check if one CTE works correctly? Run it separately:

```sql
-- Test CTE 1 only
SELECT 
    customer_id,
    SUM(sale_amount) as total_sales
FROM orders
GROUP BY customer_id;

-- Test CTE 2 only
SELECT 
    customer_id,
    MAX(order_date) as last_order
FROM orders
GROUP BY customer_id;
```

This helps you debug and make sure each piece works before combining!

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Using WITH for second CTE
```sql
WITH CTE_1 AS (...)
WITH CTE_2 AS (...)  -- WRONG! Use comma instead
```

### ❌ Mistake 2: Adding comma to last CTE
```sql
WITH CTE_1 AS (...),
CTE_2 AS (...),  -- WRONG! Remove this comma
SELECT ...
```

### ✅ Correct Way:
```sql
WITH CTE_1 AS (...),
CTE_2 AS (...)  -- No comma!
SELECT ...
```

---

## Benefits of Multiple CTEs

🎯 **Organized Code** - Each CTE solves one problem  
🎯 **Easy to Extend** - Just add another CTE with comma  
🎯 **Easy to Debug** - Test each CTE separately  
🎯 **Readable** - Clear structure, easy to understand  
🎯 **Reusable** - Each CTE result can be used multiple times  

---

## Key Takeaways

📌 **First CTE** = Uses `WITH` keyword  
📌 **Other CTEs** = Separated by **COMMA** (`,`)  
📌 **Last CTE** = NO comma after it  
📌 **All CTEs are independent** = Each queries database directly  
📌 **Main query combines** = Uses all CTEs together  
📌 **Test separately** = Run each CTE alone to verify  

---

## Practice Challenge

Create 3 CTEs to analyze this data:

**Library_Visits:**
```
student_id | visit_date  | books_read
-----------|-------------|------------
1          | 2024-01-10  | 2
1          | 2024-01-15  | 3
2          | 2024-01-12  | 1
3          | 2024-01-20  | 4
```

**Your task:**
1. CTE 1: Total visits per student
2. CTE 2: Total books read per student
3. CTE 3: Average books per visit per student
4. Main query: Combine all with student names!

Try it yourself! 📚
