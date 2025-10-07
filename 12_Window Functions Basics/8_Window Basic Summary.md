# SQL Window Functions: Complete Summary
## A Simple Guide for 7th Grade Students

## What Are Window Functions? 🪟

**Window functions** perform calculations (like totals and averages) while keeping ALL the details of your data!

**Think of it like this:**
- You're in a classroom with 30 students
- You want to see each student's test score
- AND see the class average next to each score
- Window functions show BOTH at the same time!

---

## Window Functions vs GROUP BY 🆚

### GROUP BY - Simple Analysis
- Combines rows into fewer rows
- Loses individual details
- Simpler calculations
- Perfect for basic questions

**Example:** "What's the total sales for each product?"

### Window Functions - Advanced Analysis
- Keeps all rows
- Shows details + calculations
- More powerful and dynamic
- Perfect for complex questions

**Example:** "Show each order's details AND the product's total sales"

---

## When to Use Each?

| Situation | Use This |
|-----------|----------|
| Simple totals, no details needed | GROUP BY |
| Need calculations + row details | Window Functions |
| Complex analysis with multiple levels | Window Functions |
| Basic summaries | GROUP BY |

---

## Can You Use Both Together? ✅

**YES!** But follow this rule:
- Columns in window functions must be in GROUP BY
- OR use aggregated columns

**Pattern:**
1. First: Write GROUP BY query
2. Second: Add window functions
3. Use same columns

---

## Complete Example: GROUP BY + Window Functions

### Sample Data
```
order_id | customer_id | product_id | sales
---------|-------------|------------|-------
1        | 1           | 101        | 50
2        | 1           | 101        | 30
3        | 2           | 101        | 80
4        | 2           | 102        | 60
5        | 3           | 101        | 90
6        | 3           | 102        | 70
```

### Task: Rank customers by their total sales

**Step 1: GROUP BY for totals**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales
FROM orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | total_sales
------------|-------------
1           | 80
2           | 140
3           | 160
```

**Step 2: Add Window Function for ranking**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    RANK() OVER(ORDER BY SUM(sales) DESC) AS customer_rank
FROM orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | total_sales | customer_rank
------------|-------------|---------------
3           | 160         | 1
2           | 140         | 2
1           | 80          | 3
```

**Perfect!** We used GROUP BY for totals, then window function for ranking! 🎯

---

## The Two Main Parts of Window Functions

```sql
FUNCTION(expression) OVER(window_definition)
```

### Part 1: Window Function
- SUM, AVG, COUNT, MAX, MIN (Aggregate)
- RANK, ROW_NUMBER, DENSE_RANK (Ranking)
- LEAD, LAG, FIRST_VALUE, LAST_VALUE (Value/Analytic)

### Part 2: Window Definition (OVER Clause)
Has 3 optional parts:
1. **PARTITION BY** - Divide into groups
2. **ORDER BY** - Sort the data
3. **FRAME** - Choose specific rows

---

## Complete Syntax Breakdown

```sql
SUM(sales) OVER(
    PARTITION BY product_id    -- Optional: Create groups
    ORDER BY order_date        -- Optional: Sort data
    ROWS BETWEEN ...           -- Optional: Define frame
)
```

---

## Example: All Components Together

### Sample Data
```
order_id | product_id | order_date | sales
---------|------------|------------|-------
1        | 101        | 2024-01-01 | 50
2        | 101        | 2024-01-02 | 30
3        | 101        | 2024-01-03 | 60
4        | 102        | 2024-01-01 | 80
5        | 102        | 2024-01-02 | 40
6        | 102        | 2024-01-03 | 70
```

### Query Using All Three Parts
```sql
SELECT 
    order_id,
    product_id,
    order_date,
    sales,
    SUM(sales) OVER(
        PARTITION BY product_id              -- Part 1: Group by product
        ORDER BY order_date                   -- Part 2: Sort by date
        ROWS BETWEEN UNBOUNDED PRECEDING      -- Part 3: Running total
            AND CURRENT ROW
    ) AS running_total_by_product
FROM orders;
```

**Output:**
```
order_id | product_id | order_date | sales | running_total_by_product
---------|------------|------------|-------|-------------------------
1        | 101        | 2024-01-01 | 50    | 50
2        | 101        | 2024-01-02 | 30    | 80
3        | 101        | 2024-01-03 | 60    | 140
4        | 102        | 2024-01-01 | 80    | 80
5        | 102        | 2024-01-02 | 40    | 120
6        | 102        | 2024-01-03 | 70    | 190
```

**Explanation:**
- **PARTITION BY product_id**: Separate calculations for each product
- **ORDER BY order_date**: Process in date order
- **ROWS BETWEEN...**: Running total (cumulative sum)
- Product 101: 50 → 80 (50+30) → 140 (50+30+60)
- Product 102: 80 → 120 (80+40) → 190 (80+40+70)

---

## The 4 Important Rules ⚠️

### Rule 1: Use Only in SELECT and ORDER BY
✅ **Allowed:**
```sql
SELECT ..., SUM(sales) OVER() AS total
FROM orders
ORDER BY SUM(sales) OVER();
```

❌ **Not Allowed:**
```sql
-- Cannot use in WHERE
WHERE SUM(sales) OVER() > 100

-- Cannot use in GROUP BY
GROUP BY SUM(sales) OVER()
```

---

### Rule 2: No Nesting Window Functions
❌ **Not Allowed:**
```sql
-- Cannot put window function inside another
SUM(AVG(sales) OVER()) OVER()
```

✅ **Use separately:**
```sql
AVG(sales) OVER() AS avg_sales,
SUM(sales) OVER() AS total_sales
```

---

### Rule 3: Window Functions Execute AFTER WHERE
**Execution Order:**
1. WHERE (filters data)
2. GROUP BY (aggregates)
3. Window Functions (calculate)
4. ORDER BY (sorts results)

**Example:**
```sql
SELECT 
    product_id,
    SUM(sales) OVER() AS total
FROM orders
WHERE product_id = 101;  -- Filters first, then calculates
```

---

### Rule 4: With GROUP BY, Use Same Columns
✅ **Correct:**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total,
    RANK() OVER(ORDER BY SUM(sales) DESC) AS rank
FROM orders
GROUP BY customer_id;  -- SUM(sales) is in the aggregation
```

❌ **Wrong:**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total,
    RANK() OVER(ORDER BY sales DESC) AS rank  -- Error! 'sales' not in GROUP BY
FROM orders
GROUP BY customer_id;
```

---

## Practical Example: Comparing Students

### Sample Data
```
student_id | class | test_score
-----------|-------|------------
1          | 7A    | 85
2          | 7A    | 92
3          | 7A    | 78
4          | 7B    | 88
5          | 7B    | 95
6          | 7B    | 82
```

### Query: Show each score + class average
```sql
SELECT 
    student_id,
    class,
    test_score,
    AVG(test_score) OVER(PARTITION BY class) AS class_average,
    RANK() OVER(PARTITION BY class ORDER BY test_score DESC) AS rank_in_class
FROM students;
```

**Output:**
```
student_id | class | test_score | class_average | rank_in_class
-----------|-------|------------|---------------|---------------
2          | 7A    | 92         | 85            | 1
1          | 7A    | 85         | 85            | 2
3          | 7A    | 78         | 85            | 3
5          | 7B    | 95         | 88.33         | 1
4          | 7B    | 88         | 88.33         | 2
6          | 7B    | 82         | 88.33         | 3
```

**Amazing!** Each student sees:
- Their own score
- Their class average
- Their rank within their class

---

## Summary Chart 📊

| Feature | GROUP BY | Window Functions |
|---------|----------|------------------|
| **Keeps all rows** | ❌ No | ✅ Yes |
| **Shows details** | ❌ No | ✅ Yes |
| **Good for** | Simple summaries | Complex analysis |
| **Functions** | SUM, AVG, COUNT, MAX, MIN only | Aggregate + Ranking + Analytic |
| **Use together?** | N/A | ✅ Yes, with same columns |

---

## Key Takeaways 🎓

1. **Window Functions = Calculations + Details** (no row loss!)
2. **GROUP BY = Simple summaries** (rows combine)
3. **Can mix both** (with same columns rule)
4. **Three parts in OVER:** PARTITION BY, ORDER BY, FRAME (all optional)
5. **Use in SELECT and ORDER BY only**
6. **Executes after WHERE** (filters happen first)

---

## Your Next Steps 🚀

Now that you understand the basics, you'll learn:
- Aggregate window functions (SUM, AVG, COUNT)
- Ranking functions (RANK, ROW_NUMBER)
- Analytic functions (LEAD, LAG)
- Real-world use cases

**Remember:** Window functions are like having X-ray vision for your data - you see everything at once! 👁️✨
