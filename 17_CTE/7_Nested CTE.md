# Nested CTEs - Building Blocks on Top of Each Other!
*When One Helper Needs Another Helper's Work*

## What is a Nested CTE?

A **Nested CTE** is like a CTE inside another CTE - it's when one helper uses another helper's results!

Think of it like baking a cake:
- **Step 1 (Standalone CTE):** Mix flour, eggs, and sugar
- **Step 2 (Nested CTE):** Use that mixture to bake the cake
- **Step 3 (Main Query):** Decorate the cake

Each step **depends** on the previous step!

---

## Standalone vs Nested CTE

| Feature | Standalone CTE | Nested CTE |
|---------|----------------|------------|
| Independence | Works alone | Needs another CTE |
| Data Source | Queries database directly | Queries another CTE |
| Can run separately? | ✅ Yes | ❌ No |
| Depends on others? | ❌ No | ✅ Yes |

---

## How It Works (The Chain)

```
DATABASE → CTE 1 (Standalone) → CTE 2 (Nested) → CTE 3 (Nested) → MAIN QUERY → FINAL RESULT
```

**The Flow:**
1. CTE 1 queries the database (standalone)
2. CTE 2 uses CTE 1's results (nested)
3. CTE 3 uses CTE 2's results (nested)
4. Main query combines everything
5. We get the final output!

---

## Basic Syntax

```sql
-- CTE 1: Standalone (queries database)
WITH CTE_1 AS (
    SELECT columns
    FROM database_table
),

-- CTE 2: Nested (queries CTE 1)
CTE_2 AS (
    SELECT columns
    FROM CTE_1  -- ← Uses CTE 1!
)

-- Main Query
SELECT * FROM CTE_2;
```

**Key Point:** CTE 2 uses `FROM CTE_1` instead of a database table!

---

## Simple Example: Student Grades Report

### Our Table: Exam_Scores
```
student_id | student_name | subject | score
-----------|--------------|---------|------
1          | Alice        | Math    | 85
1          | Alice        | Science | 92
2          | Bob          | Math    | 78
2          | Bob          | Science | 88
3          | Charlie      | Math    | 95
3          | Charlie      | Science | 90
```

---

### Task: Rank Students by Average Score

**Step 1:** Calculate average scores (Standalone CTE)  
**Step 2:** Rank students based on those averages (Nested CTE)

```sql
-- CTE 1: Calculate average score per student (Standalone)
WITH StudentAverage AS (
    SELECT 
        student_id,
        student_name,
        AVG(score) as avg_score
    FROM exam_scores
    GROUP BY student_id, student_name
),

-- CTE 2: Rank students based on average (Nested - uses CTE 1)
RankedStudents AS (
    SELECT 
        student_id,
        student_name,
        avg_score,
        RANK() OVER (ORDER BY avg_score DESC) as student_rank
    FROM StudentAverage  -- ← Using CTE 1!
)

-- Main Query: Display final results
SELECT 
    student_rank,
    student_name,
    ROUND(avg_score, 2) as average_score
FROM RankedStudents
ORDER BY student_rank;
```

**Expected Output:**
```
student_rank | student_name | average_score
-------------|--------------|---------------
1            | Charlie      | 92.50
2            | Bob          | 83.00
3            | Alice        | 88.50
```

**What Happened:**
1. **CTE 1 (StudentAverage):** Calculated average for each student
2. **CTE 2 (RankedStudents):** Used CTE 1 to rank students
3. **Main Query:** Displayed the final ranked list

---

## Important: Testing Nested CTEs

❌ **You CANNOT run a nested CTE alone:**
```sql
-- This will give an ERROR!
SELECT * FROM RankedStudents;
```

✅ **You MUST include the CTE it depends on:**
```sql
WITH StudentAverage AS (
    SELECT student_id, student_name, AVG(score) as avg_score
    FROM exam_scores
    GROUP BY student_id, student_name
)
SELECT * FROM RankedStudents;  -- Still error - RankedStudents not defined!
```

✅ **Correct way to test:**
```sql
-- Include ALL CTEs, comment out main query
WITH StudentAverage AS (...),
RankedStudents AS (...)
-- SELECT * FROM RankedStudents;  -- Commented out
SELECT * FROM RankedStudents;  -- Test here instead
```

---

## Real Project: Customer Analysis Report

Let's build a complete customer report step by step!

### Our Tables:

**Orders:**
```
order_id | customer_id | order_date | sale_amount
---------|-------------|------------|------------
1        | 1           | 2024-01-15 | 120
2        | 1           | 2024-02-20 | 80
3        | 2           | 2024-01-10 | 200
4        | 3           | 2024-03-05 | 150
5        | 3           | 2024-03-15 | 90
```

**Customers:**
```
customer_id | first_name | last_name
------------|------------|----------
1           | Alice      | Smith
2           | Bob        | Jones
3           | Charlie    | Brown
```

---

### Step 1: Calculate Total Sales (Standalone CTE)

```sql
WITH TotalSales AS (
    SELECT 
        customer_id,
        SUM(sale_amount) as total_sales
    FROM orders
    GROUP BY customer_id
)
SELECT * FROM TotalSales;
```

**Intermediate Output:**
```
customer_id | total_sales
------------|------------
1           | 200
2           | 200
3           | 240
```

---

### Step 2: Rank Customers (Nested CTE)

```sql
-- CTE 1: Total Sales (Standalone)
WITH TotalSales AS (
    SELECT 
        customer_id,
        SUM(sale_amount) as total_sales
    FROM orders
    GROUP BY customer_id
),

-- CTE 2: Customer Ranking (Nested - uses CTE 1)
CustomerRank AS (
    SELECT 
        customer_id,
        total_sales,
        RANK() OVER (ORDER BY total_sales DESC) as sales_rank
    FROM TotalSales  -- ← Reusing CTE 1!
)

-- Main Query
SELECT * FROM CustomerRank;
```

**Output:**
```
customer_id | total_sales | sales_rank
------------|-------------|------------
3           | 240         | 1
1           | 200         | 2
2           | 200         | 2
```

---

### Step 3: Segment Customers (Another Nested CTE)

```sql
-- CTE 1: Total Sales (Standalone)
WITH TotalSales AS (
    SELECT 
        customer_id,
        SUM(sale_amount) as total_sales
    FROM orders
    GROUP BY customer_id
),

-- CTE 2: Customer Ranking (Nested)
CustomerRank AS (
    SELECT 
        customer_id,
        total_sales,
        RANK() OVER (ORDER BY total_sales DESC) as sales_rank
    FROM TotalSales
),

-- CTE 3: Customer Segmentation (Nested - uses CTE 1)
CustomerSegment AS (
    SELECT 
        customer_id,
        total_sales,
        CASE 
            WHEN total_sales > 220 THEN 'High'
            WHEN total_sales > 150 THEN 'Medium'
            ELSE 'Low'
        END as segment
    FROM TotalSales  -- ← Reusing CTE 1 again!
)

-- Main Query: Combine everything
SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    ts.total_sales,
    cr.sales_rank,
    cs.segment
FROM customers c
LEFT JOIN TotalSales ts ON c.customer_id = ts.customer_id
LEFT JOIN CustomerRank cr ON c.customer_id = cr.customer_id
LEFT JOIN CustomerSegment cs ON c.customer_id = cs.customer_id
ORDER BY c.customer_id;
```

**Final Output:**
```
customer_id | first_name | last_name | total_sales | sales_rank | segment
------------|------------|-----------|-------------|------------|--------
1           | Alice      | Smith     | 200         | 2          | Medium
2           | Bob        | Jones     | 200         | 2          | Medium
3           | Charlie    | Brown     | 240         | 1          | High
```

---

## Another Example: Library Reading Progress

### Our Table: Book_Loans
```
student_id | book_name     | pages | days_borrowed
-----------|---------------|-------|---------------
1          | Math Guide    | 200   | 10
1          | Science Fun   | 150   | 7
2          | Space Atlas   | 300   | 14
3          | History Book  | 250   | 12
```

### Task: Calculate Reading Speed and Rank Students

```sql
-- CTE 1: Calculate total pages read (Standalone)
WITH TotalPages AS (
    SELECT 
        student_id,
        SUM(pages) as total_pages,
        SUM(days_borrowed) as total_days
    FROM book_loans
    GROUP BY student_id
),

-- CTE 2: Calculate reading speed (Nested - uses CTE 1)
ReadingSpeed AS (
    SELECT 
        student_id,
        total_pages,
        total_days,
        ROUND(total_pages * 1.0 / total_days, 2) as pages_per_day
    FROM TotalPages  -- ← Using CTE 1
),

-- CTE 3: Rank by reading speed (Nested - uses CTE 2)
RankedReaders AS (
    SELECT 
        student_id,
        pages_per_day,
        RANK() OVER (ORDER BY pages_per_day DESC) as speed_rank
    FROM ReadingSpeed  -- ← Using CTE 2
)

-- Main Query
SELECT * FROM RankedReaders
ORDER BY speed_rank;
```

**Expected Output:**
```
student_id | pages_per_day | speed_rank
-----------|---------------|------------
1          | 20.59         | 1
3          | 20.83         | 2
2          | 21.43         | 3
```

---

## Why Use Nested CTEs?

### ✅ Benefits:
- **No Repetition:** Reuse previous CTE results
- **Organized:** Each CTE solves one problem
- **Readable:** Clear step-by-step logic
- **Modular:** Easy to add or modify steps

### ❌ Drawback:
- **Cannot test alone:** Must run with dependent CTEs

---

## Key Differences Summary

```sql
-- STANDALONE: Queries database directly
WITH Standalone AS (
    SELECT * FROM database_table  -- ← From database
)

-- NESTED: Queries another CTE
WITH Nested AS (
    SELECT * FROM Standalone  -- ← From CTE
)
```

---

## Common Pattern

```sql
WITH 
-- Step 1: Get raw data (Standalone)
RawData AS (
    SELECT * FROM orders
),

-- Step 2: Calculate something (Nested)
Calculations AS (
    SELECT *, SUM(amount) FROM RawData
),

-- Step 3: Rank or categorize (Nested)
FinalResults AS (
    SELECT *, RANK() FROM Calculations
)

-- Main Query
SELECT * FROM FinalResults;
```

---

## Key Takeaways

📌 **Standalone CTE** = Queries database directly (independent)  
📌 **Nested CTE** = Queries another CTE (dependent)  
📌 **Chain building** = Each step builds on previous  
📌 **Reuse logic** = Don't repeat calculations  
📌 **Cannot test alone** = Must include all dependent CTEs  
📌 **Modularity** = One CTE = One clear purpose  

---

## Practice Challenge

Create nested CTEs for this scenario:

**Sales_Data:**
```
product_id | product_name | quantity_sold | price
-----------|--------------|---------------|------
1          | Notebook     | 50            | 2
2          | Pen          | 100           | 1
3          | Eraser       | 75            | 0.5
```

**Your Tasks:**
1. CTE 1: Calculate total revenue per product
2. CTE 2: Rank products by revenue
3. CTE 3: Categorize as 'Top', 'Medium', or 'Low' seller
4. Main Query: Display complete report!

Try building it step by step! 🎯
