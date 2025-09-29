# ROW_NUMBER Real-World Use Cases

## Why ROW_NUMBER is Most Used

According to real project experience, **ROW_NUMBER is used much more often** than RANK or DENSE_RANK!

Let's learn the most important use cases with step-by-step examples.

---

## Use Case 1: Top-N Analysis

**What is it?** Finding the best performers or highest values

**Business Question:** "Show me the TOP 3 products" or "Find the BEST 5 customers"

### Task Example:
**"Find the top highest sales for each product"**

---

### Step-by-Step Solution

**Step 1: Get the basic data**
```sql
SELECT 
    order_id,
    product_id,
    sales
FROM sales_orders;
```

**Results:**
```
Order ID | Product ID | Sales
---------|------------|------
8        | 101        | 90
4        | 101        | 20
1        | 101        | 10
5        | 101        | 60
7        | 102        | 60
...
```

---

**Step 2: Add ranking within each product**
```sql
SELECT 
    order_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS rank_by_product
FROM sales_orders;
```

**Results:**
```
Order ID | Product ID | Sales | Rank by Product
---------|------------|-------|----------------
8        | 101        | 90    | 1  ← Highest for 101
5        | 101        | 60    | 2
4        | 101        | 20    | 3
1        | 101        | 10    | 4
---------|------------|-------|----------------
7        | 102        | 60    | 1  ← Highest for 102
6        | 102        | 40    | 2
3        | 102        | 15    | 3
---------|------------|-------|----------------
10       | 103        | 60    | 1  ← Highest for 103
9        | 103        | 20    | 2
```

**What happened?**
- Data divided by product_id (PARTITION BY)
- Within each product, sorted by sales (highest first)
- Each product gets its own ranking (1, 2, 3...)

---

**Step 3: Filter to get only rank 1 (the highest)**
```sql
SELECT * FROM (
    SELECT 
        order_id,
        product_id,
        sales,
        ROW_NUMBER() OVER (
            PARTITION BY product_id 
            ORDER BY sales DESC
        ) AS rank_by_product
    FROM sales_orders
) 
WHERE rank_by_product = 1;
```

**Final Results:**
```
Order ID | Product ID | Sales | Rank by Product
---------|------------|-------|----------------
8        | 101        | 90    | 1
7        | 102        | 60    | 1
10       | 103        | 60    | 1
```

**Success!** We found the highest sale for each product!

---

### Understanding the Query

**Why use subquery?**
We can't use WHERE with window functions directly, so we:
1. Create the ranking (inner query)
2. Filter the results (outer query)

**The pattern:**
```sql
SELECT * FROM (
    -- Inner query: Create ranking
    SELECT ..., ROW_NUMBER() OVER (...) AS rank
    FROM table
) 
WHERE rank = 1;  -- Outer query: Filter
```

---

### When to Use Top-N Analysis?

**Business situations:**
- "Show me the top 5 best-selling products"
- "Who are our top 10 customers?"
- "What are the 3 highest-rated items?"
- "Find the best performer in each department"

**Why it's important:**
- Focus on what works best
- Reward top performers
- Make smart business decisions
- Prioritize resources

---

## Use Case 2: Bottom-N Analysis

**What is it?** Finding the worst performers or lowest values

**Business Question:** "Show me the BOTTOM 2 customers" or "Find the WORST 5 products"

### Task Example:
**"Find the lowest two customers based on their total sales"**

**Why do businesses need this?**
- Cut costs
- Identify problems
- Stop selling unprofitable products
- Focus improvement efforts

---

### Step-by-Step Solution

**Step 1: Get customer sales data**
```sql
SELECT 
    order_id,
    customer_id,
    sales
FROM sales_orders;
```

**Results:**
```
Order ID | Customer ID | Sales
---------|-------------|------
1        | 3           | 10
2        | 2           | 20
3        | 4           | 15
4        | 3           | 20
...
```

Multiple rows per customer - we need TOTAL per customer!

---

**Step 2: Calculate total sales per customer (GROUP BY)**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales
FROM sales_orders
GROUP BY customer_id;
```

**Results:**
```
Customer ID | Total Sales
------------|------------
1           | 120
2           | 35
3           | 125
4           | 90
```

Now we have one row per customer with their total!

---

**Step 3: Add ranking from lowest to highest**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    ROW_NUMBER() OVER (
        ORDER BY SUM(sales) ASC
    ) AS rank_customers
FROM sales_orders
GROUP BY customer_id;
```

**Results:**
```
Customer ID | Total Sales | Rank Customers
------------|-------------|---------------
2           | 35          | 1  ← Lowest
4           | 90          | 2
1           | 120         | 3
3           | 125         | 4  ← Highest
```

**Important:** We use `ORDER BY SUM(sales) ASC` (ascending) because we want lowest first!

---

**Step 4: Filter to get bottom 2**
```sql
SELECT * FROM (
    SELECT 
        customer_id,
        SUM(sales) AS total_sales,
        ROW_NUMBER() OVER (
            ORDER BY SUM(sales) ASC
        ) AS rank_customers
    FROM sales_orders
    GROUP BY customer_id
)
WHERE rank_customers <= 2;
```

**Final Results:**
```
Customer ID | Total Sales | Rank Customers
------------|-------------|---------------
2           | 35          | 1
4           | 90          | 2
```

**Success!** We found the 2 customers with lowest total sales!

---

### Important Rule: GROUP BY + Window Functions

**When using both together:**
Only use columns that appear in GROUP BY clause!

**Correct:**
```sql
SELECT 
    customer_id,           -- In GROUP BY ✓
    SUM(sales),            -- Aggregate function ✓
    ROW_NUMBER() OVER (ORDER BY SUM(sales))  -- Uses aggregate ✓
FROM sales_orders
GROUP BY customer_id;      -- Groups by customer_id
```

**Wrong:**
```sql
SELECT 
    customer_id,
    order_id,              -- NOT in GROUP BY ✗
    SUM(sales)
FROM sales_orders
GROUP BY customer_id;
```

---

### When to Use Bottom-N Analysis?

**Business situations:**
- "Which 5 products sell the least?"
- "Who are our 3 weakest salespeople?"
- "What are the lowest-rated items?"
- "Find underperforming departments"

**Why it's important:**
- Identify problems early
- Cut losses
- Improve weak areas
- Save money

---

## Top-N vs Bottom-N: The Difference

| Aspect | Top-N Analysis | Bottom-N Analysis |
|--------|----------------|-------------------|
| **Goal** | Find BEST | Find WORST |
| **Sort** | DESC (highest first) | ASC (lowest first) |
| **Business use** | Reward, focus on success | Fix, cut costs |
| **Example** | Top 5 products | Bottom 3 sellers |

**Same technique, different direction!**

---

## Complete Comparison Example

### Data: Customer Total Sales
```
Customer | Total Sales
---------|------------
A        | 150
B        | 80
C        | 200
D        | 80
E        | 120
```

### Top 2 (Highest):
```sql
ORDER BY total_sales DESC
```
**Result:** C (200), A (150)

### Bottom 2 (Lowest):
```sql
ORDER BY total_sales ASC
```
**Result:** B (80), D (80)

---

## The Subquery Pattern

**Template for Top/Bottom-N queries:**

```sql
SELECT * FROM (
    -- Inner query: Create ranking
    SELECT 
        columns,
        ROW_NUMBER() OVER (
            [PARTITION BY group_column]
            ORDER BY sort_column [DESC/ASC]
        ) AS rank_name
    FROM table
    [GROUP BY columns]
) 
WHERE rank_name <= N;  -- N = how many you want
```

**Replace:**
- `N` = how many you want (1, 2, 3, 5, 10...)
- `DESC` = for TOP-N (highest first)
- `ASC` = for BOTTOM-N (lowest first)

---

## Real-World Examples

### Example 1: School Rankings
**Top 3 students per class:**
```sql
SELECT * FROM (
    SELECT 
        student_name,
        class,
        marks,
        ROW_NUMBER() OVER (
            PARTITION BY class 
            ORDER BY marks DESC
        ) AS rank
    FROM students
) 
WHERE rank <= 3;
```

### Example 2: E-commerce
**Top 5 selling products:**
```sql
SELECT * FROM (
    SELECT 
        product_name,
        SUM(quantity) AS total_sold,
        ROW_NUMBER() OVER (
            ORDER BY SUM(quantity) DESC
        ) AS rank
    FROM orders
    GROUP BY product_name
) 
WHERE rank <= 5;
```

---

## Key Points to Remember

1. **ROW_NUMBER is most commonly used** in real projects
2. **Top-N Analysis** = Find the BEST (ORDER BY DESC)
3. **Bottom-N Analysis** = Find the WORST (ORDER BY ASC)
4. **Use subqueries** to filter by rank
5. **Can combine with GROUP BY** for aggregations
6. **PARTITION BY** to rank within groups
7. **Very important for business decisions!**

---

## Practice Questions

1. What's the difference between Top-N and Bottom-N analysis?
2. Why do we need a subquery to filter by rank?
3. What does ORDER BY DESC do vs ASC?
4. If you want top 5 products per category, what do you use?
5. When combining GROUP BY and window functions, what's the rule?

---

## Quick Decision Guide

**Need to find:**
- Best performers? → Top-N (ORDER BY DESC)
- Worst performers? → Bottom-N (ORDER BY ASC)
- Per category? → Add PARTITION BY
- With totals? → Use GROUP BY first

**Always use ROW_NUMBER for Top/Bottom-N analysis!**
