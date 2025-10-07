# SQL Window Functions: FIRST_VALUE & LAST_VALUE
### Accessing First and Last Values in Your Data

## What are FIRST_VALUE and LAST_VALUE?

These functions help you access values from specific positions in your data:

- **FIRST_VALUE** = Get the value from the FIRST row in a group
- **LAST_VALUE** = Get the value from the LAST row in a group

Think of it like looking at a line of students: FIRST_VALUE looks at the first student, LAST_VALUE looks at the last student!

---

## Basic Syntax

### FIRST_VALUE Function
```sql
FIRST_VALUE(column_name) 
OVER (PARTITION BY group_column ORDER BY sort_column)
```

### LAST_VALUE Function
```sql
LAST_VALUE(column_name) 
OVER (
    PARTITION BY group_column 
    ORDER BY sort_column
    ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
)
```

**Important Note:** LAST_VALUE needs extra frame definition to work correctly!

---

## Example: Finding Lowest and Highest Sales

### Step 1: Create Sample Data

```sql
CREATE TABLE sales_orders (
    order_id INT,
    product_id INT,
    sales DECIMAL(10,2)
);

INSERT INTO sales_orders VALUES
(1, 101, 10.00),
(2, 101, 50.00),
(3, 101, 90.00),
(4, 102, 25.00),
(5, 102, 60.00),
(6, 103, 30.00),
(7, 104, 45.00);
```

**Our Data:**
| order_id | product_id | sales |
|----------|------------|-------|
| 1        | 101        | 10.00 |
| 2        | 101        | 50.00 |
| 3        | 101        | 90.00 |
| 4        | 102        | 25.00 |
| 5        | 102        | 60.00 |
| 6        | 103        | 30.00 |
| 7        | 104        | 45.00 |

---

### Step 2: Find LOWEST Sales for Each Product

```sql
SELECT 
    order_id,
    product_id,
    sales,
    FIRST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales
    ) AS lowest_sales
FROM sales_orders
ORDER BY product_id, sales;
```

**Output:**
| order_id | product_id | sales | lowest_sales |
|----------|------------|-------|--------------|
| 1        | 101        | 10.00 | 10.00        |
| 2        | 101        | 50.00 | 10.00        |
| 3        | 101        | 90.00 | 10.00        |
| 4        | 102        | 25.00 | 25.00        |
| 5        | 102        | 60.00 | 25.00        |
| 6        | 103        | 30.00 | 30.00        |
| 7        | 104        | 45.00 | 45.00        |

**What happened?**
- Data sorted by sales (lowest to highest)
- FIRST_VALUE picks the first row = lowest sales
- Product 101: Lowest = 10.00 (shown in all rows)
- Product 102: Lowest = 25.00 (shown in all rows)

---

### Step 3: Find HIGHEST Sales (Method 1 - Using LAST_VALUE)

```sql
SELECT 
    order_id,
    product_id,
    sales,
    FIRST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales
    ) AS lowest_sales,
    LAST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales
        ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
    ) AS highest_sales
FROM sales_orders
ORDER BY product_id, sales;
```

**Output:**
| order_id | product_id | sales | lowest_sales | highest_sales |
|----------|------------|-------|--------------|---------------|
| 1        | 101        | 10.00 | 10.00        | 90.00         |
| 2        | 101        | 50.00 | 10.00        | 90.00         |
| 3        | 101        | 90.00 | 10.00        | 90.00         |
| 4        | 102        | 25.00 | 25.00        | 60.00         |
| 5        | 102        | 60.00 | 25.00        | 60.00         |
| 6        | 103        | 30.00 | 30.00        | 30.00         |
| 7        | 104        | 45.00 | 45.00        | 45.00         |

**What happened?**
- LAST_VALUE needs special frame definition
- `ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING` = look from current row to end
- Product 101: Highest = 90.00
- Product 102: Highest = 60.00

---

### Step 4: Find HIGHEST Sales (Method 2 - Easier Way!)

Instead of using LAST_VALUE, we can use FIRST_VALUE with reverse sorting:

```sql
SELECT 
    order_id,
    product_id,
    sales,
    FIRST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales ASC
    ) AS lowest_sales,
    FIRST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS highest_sales
FROM sales_orders
ORDER BY product_id, sales;
```

**Output:** (Same as above)

**Why is this easier?**
- Just change `ORDER BY sales DESC` (highest to lowest)
- FIRST_VALUE now picks the highest value!
- No need for complex frame definition

---

### Step 5: Find HIGHEST Sales (Method 3 - Using MAX)

```sql
SELECT 
    order_id,
    product_id,
    sales,
    MIN(sales) OVER (PARTITION BY product_id) AS lowest_sales,
    MAX(sales) OVER (PARTITION BY product_id) AS highest_sales
FROM sales_orders
ORDER BY product_id, sales;
```

**Output:** (Same as above)

**What happened?**
- MIN = Automatically finds minimum
- MAX = Automatically finds maximum
- No need to sort with ORDER BY!

---

## Real-World Use Case: Sales Performance Analysis

Let's calculate how far each sale is from the lowest sale:

```sql
SELECT 
    order_id,
    product_id,
    sales AS current_sales,
    FIRST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales
    ) AS lowest_sales,
    sales - FIRST_VALUE(sales) OVER (
        PARTITION BY product_id 
        ORDER BY sales
    ) AS sales_difference
FROM sales_orders
ORDER BY product_id, sales;
```

**Output:**
| order_id | product_id | current_sales | lowest_sales | sales_difference |
|----------|------------|---------------|--------------|------------------|
| 1        | 101        | 10.00         | 10.00        | 0.00             |
| 2        | 101        | 50.00         | 10.00        | 40.00            |
| 3        | 101        | 90.00         | 10.00        | 80.00            |
| 4        | 102        | 25.00         | 25.00        | 0.00             |
| 5        | 102        | 60.00         | 25.00        | 35.00            |
| 6        | 103        | 30.00         | 30.00        | 0.00             |
| 7        | 104        | 45.00         | 45.00        | 0.00             |

**Business Insight:**
- Shows how much better each sale performed vs the worst sale
- Order 3 (Product 101): $80 better than worst sale!
- Helps identify best-performing orders

---

## Three Ways to Find Extremes

| Method | Function | Pros | Cons |
|--------|----------|------|------|
| **Method 1** | LAST_VALUE | Direct approach | Needs frame definition |
| **Method 2** | FIRST_VALUE (DESC) | Simple, no frame needed | Need to remember DESC |
| **Method 3** | MIN/MAX | Simplest! | Limited to min/max only |

**Recommendation:** Use MIN/MAX for simple cases, FIRST_VALUE for more control!

---

## Key Points to Remember

1. **FIRST_VALUE** works great with default settings ✅
2. **LAST_VALUE** needs frame definition (use FIRST_VALUE instead!) 
3. **PARTITION BY** = Create separate groups
4. **ORDER BY** = Sort within each group (REQUIRED)
5. Use **MIN/MAX** for simplest extreme value calculations

---

## Practice Exercise

Create a table with student test scores for different subjects. Find:
- Lowest score per subject
- Highest score per subject
- Difference between each score and the lowest score

Try all three methods! 🎯
