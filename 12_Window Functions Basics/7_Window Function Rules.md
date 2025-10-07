# SQL Window Functions: Rules and Limitations
## A Simple Guide for 7th Grade Students

## The 4 Important Rules of Window Functions 📜

When using window functions, you **must follow these rules**, or SQL will give you errors!

---

## Rule 1: Window Functions Can ONLY Be Used in SELECT and ORDER BY ✅

### ✅ Allowed Places

**In SELECT clause:**
```sql
SELECT 
    order_id,
    sales,
    SUM(sales) OVER(PARTITION BY product_id) AS total_sales
FROM orders;
```

**In ORDER BY clause:**
```sql
SELECT 
    order_id,
    sales,
    SUM(sales) OVER(PARTITION BY order_status) AS total_sales
FROM orders
ORDER BY SUM(sales) OVER(PARTITION BY order_status) DESC;
```

---

### ❌ NOT Allowed Places

**In WHERE clause:**
```sql
-- ❌ ERROR! Cannot filter using window functions
SELECT 
    order_id,
    sales,
    SUM(sales) OVER(PARTITION BY order_status) AS total_sales
FROM orders
WHERE SUM(sales) OVER(PARTITION BY order_status) > 100;
```

**Error Message:**
```
ERROR: window functions are not allowed in WHERE
```

**In GROUP BY clause:**
```sql
-- ❌ ERROR! Cannot group by window functions
SELECT 
    order_id,
    sales,
    SUM(sales) OVER(PARTITION BY order_status) AS total_sales
FROM orders
GROUP BY SUM(sales) OVER(PARTITION BY order_status);
```

**Error Message:**
```
ERROR: window functions are not allowed in GROUP BY
```

---

### Example: Correct Use in SELECT and ORDER BY

**Sample Data:**
```
order_id | order_status | sales
---------|--------------|-------
1        | Delivered    | 50
2        | Delivered    | 80
3        | Shipped      | 60
4        | Shipped      | 90
```

**Query:**
```sql
SELECT 
    order_id,
    order_status,
    sales,
    SUM(sales) OVER(PARTITION BY order_status) AS total_sales
FROM orders
ORDER BY SUM(sales) OVER(PARTITION BY order_status) DESC;
```

**Output:**
```
order_id | order_status | sales | total_sales
---------|--------------|-------|-------------
3        | Shipped      | 60    | 150
4        | Shipped      | 90    | 150
1        | Delivered    | 50    | 130
2        | Delivered    | 80    | 130
```

**Explanation:** Sorted by total_sales (Shipped: 150 first, Delivered: 130 second)

---

## Rule 2: You CANNOT Nest Window Functions ⛔

**Nesting** means putting one window function inside another - **NOT ALLOWED!**

### ❌ Wrong Example:
```sql
-- ❌ ERROR! Window function inside window function
SELECT 
    order_id,
    sales,
    SUM(
        AVG(sales) OVER(PARTITION BY product_id)
    ) OVER(PARTITION BY order_status) AS nested_calc
FROM orders;
```

**Error Message:**
```
ERROR: window function calls cannot be nested
```

### ✅ Correct Alternative:
Use separate window functions:
```sql
SELECT 
    order_id,
    sales,
    AVG(sales) OVER(PARTITION BY product_id) AS avg_by_product,
    SUM(sales) OVER(PARTITION BY order_status) AS total_by_status
FROM orders;
```

---

## Rule 3: Window Functions Execute AFTER WHERE Clause 🔄

**Order of Execution:**
1. First: WHERE filters the data
2. Second: Window function calculates

### Example: Filtering Before Window Function

**Sample Data:**
```
order_id | product_id | order_status | sales
---------|------------|--------------|-------
1        | 101        | Delivered    | 50
2        | 101        | Shipped      | 80
3        | 102        | Delivered    | 60
4        | 102        | Shipped      | 90
5        | 103        | Delivered    | 70
```

**Query:**
```sql
SELECT 
    order_id,
    product_id,
    order_status,
    sales,
    SUM(sales) OVER(PARTITION BY order_status) AS total_sales
FROM orders
WHERE product_id IN (101, 102);
```

**Step 1: WHERE filters first**
```
order_id | product_id | order_status | sales
---------|------------|--------------|-------
1        | 101        | Delivered    | 50
2        | 101        | Shipped      | 80
3        | 102        | Delivered    | 60
4        | 102        | Shipped      | 90
```

**Step 2: Window function calculates on filtered data**

**Output:**
```
order_id | product_id | order_status | sales | total_sales
---------|------------|--------------|-------|-------------
1        | 101        | Delivered    | 50    | 110
3        | 102        | Delivered    | 60    | 110
2        | 101        | Shipped      | 80    | 170
4        | 102        | Shipped      | 90    | 170
```

**Explanation:**
- Product 103 was filtered out by WHERE
- Delivered total: 50 + 60 = 110 (only products 101 & 102)
- Shipped total: 80 + 90 = 170 (only products 101 & 102)

---

## Rule 4: Window Functions with GROUP BY - Use Same Columns! 🎯

**You CAN use window functions with GROUP BY, BUT:**
- Columns in the window function MUST be in the GROUP BY
- OR use aggregated columns from GROUP BY

---

### Example Task: Rank Customers by Total Sales

**Sample Data:**
```
order_id | customer_id | sales
---------|-------------|-------
1        | 1           | 50
2        | 1           | 30
3        | 2           | 20
4        | 2           | 25
5        | 3           | 80
6        | 3           | 90
7        | 4           | 40
```

---

### Step 1: Create Aggregation with GROUP BY

**Query:**
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
2           | 45
3           | 170
4           | 40
```

---

### Step 2: Add Window Function to Rank

**Query:**
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
3           | 170         | 1
1           | 80          | 2
2           | 45          | 3
4           | 40          | 4
```

**✅ This works!** Because `SUM(sales)` is part of the GROUP BY calculation.

---

### ❌ What Breaks the Rule?

**Wrong Query:**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    RANK() OVER(ORDER BY sales DESC) AS customer_rank
FROM orders
GROUP BY customer_id;
```

**Error Message:**
```
ERROR: column "sales" must appear in GROUP BY clause
or be used in an aggregate function
```

**Why?** `sales` (individual values) is NOT in GROUP BY, only `customer_id` and `SUM(sales)` are!

---

### ✅ Correct Alternative 1: Use the Aggregated Column

```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    RANK() OVER(ORDER BY SUM(sales) DESC) AS customer_rank
FROM orders
GROUP BY customer_id;
```

---

### ✅ Correct Alternative 2: Use GROUP BY Column

```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    RANK() OVER(ORDER BY customer_id) AS rank_by_id
FROM orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | total_sales | rank_by_id
------------|-------------|------------
1           | 80          | 1
2           | 45          | 2
3           | 170         | 3
4           | 40          | 4
```

**✅ This works!** Because `customer_id` IS in GROUP BY.

---

## Complete Example: All Rules in Action

**Sample Data:**
```
order_id | customer_id | product_id | sales
---------|-------------|------------|-------
1        | 1           | 101        | 50
2        | 1           | 102        | 30
3        | 2           | 101        | 20
4        | 2           | 102        | 25
5        | 3           | 101        | 80
6        | 3           | 102        | 90
7        | 4           | 101        | 40
```

**Task:** Rank customers by total sales, but only for product 101

**Query:**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    RANK() OVER(ORDER BY SUM(sales) DESC) AS customer_rank
FROM orders
WHERE product_id = 101              -- Rule 3: WHERE executes first
GROUP BY customer_id
ORDER BY RANK() OVER(ORDER BY SUM(sales) DESC);  -- Rule 1: Can use in ORDER BY
```

**Step 1: WHERE filters (product 101 only)**
```
order_id | customer_id | product_id | sales
---------|-------------|------------|-------
1        | 1           | 101        | 50
3        | 2           | 101        | 20
5        | 3           | 101        | 80
7        | 4           | 101        | 40
```

**Step 2: GROUP BY aggregates**
```
customer_id | total_sales
------------|-------------
1           | 50
2           | 20
3           | 80
4           | 40
```

**Step 3: Window function ranks**

**Output:**
```
customer_id | total_sales | customer_rank
------------|-------------|---------------
3           | 80          | 1
1           | 50          | 2
4           | 40          | 3
2           | 20          | 4
```

---

## Summary: The 4 Rules 📋

| Rule | What It Means | Example |
|------|---------------|---------|
| **1** | Use only in SELECT and ORDER BY | ✅ `SELECT ... SUM() OVER()` |
| **2** | Cannot nest window functions | ❌ `SUM(AVG() OVER()) OVER()` |
| **3** | Window functions run AFTER WHERE | WHERE filters first, then calculate |
| **4** | With GROUP BY, use same columns | Use `SUM(sales)` not just `sales` |

---

## Quick Tips 💡

1. **Can't filter?** Use a subquery instead:
   ```sql
   SELECT * FROM (
       SELECT ..., SUM() OVER() AS total
       FROM table
   ) WHERE total > 100
   ```

2. **Building complex queries?**
   - First: Write the GROUP BY
   - Second: Add window functions
   - Test at each step!

3. **Remember execution order:**
   WHERE → GROUP BY → Window Functions → ORDER BY

**Remember:** Window functions are powerful but have specific rules - follow them and you'll be fine! 🌟
