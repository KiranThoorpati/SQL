# SQL Window Functions: PARTITION BY
## A Simple Guide for 7th Grade Students

## What is PARTITION BY? 🪟

**PARTITION BY** is like dividing your classroom into groups!

**Real-Life Example:**
- Your teacher wants to find the average score for each grade level
- Instead of mixing everyone together, she divides students by grade
- 7th graders in one group, 8th graders in another
- Then calculates the average for each group separately

That's exactly what PARTITION BY does with data!

---

## Two Options for Defining Windows

### Option 1: No PARTITION BY (One Big Window)

```sql
SUM(sales) OVER()
```

**Result:** Calculates over ALL rows (entire dataset = 1 window)

### Option 2: With PARTITION BY (Multiple Windows)

```sql
SUM(sales) OVER(PARTITION BY product)
```

**Result:** Divides data into groups, calculates for each group separately

---

## Visual Example: How PARTITION BY Works

### Sample Data

```
month    | product | sales
---------|---------|-------
January  | Apple   | 100
January  | Banana  | 50
February | Apple   | 120
February | Banana  | 80
```

---

### Example 1: Without PARTITION BY (One Window)

**Query:**
```sql
SELECT 
    month,
    product,
    sales,
    SUM(sales) OVER() AS total_sales
FROM sales_data;
```

**How SQL sees it:**
```
┌─────────────────────────────┐
│ ONE BIG WINDOW - ALL DATA   │
│ January  | Apple   | 100    │
│ January  | Banana  | 50     │
│ February | Apple   | 120    │
│ February | Banana  | 80     │
└─────────────────────────────┘
```

**Output:**
```
month    | product | sales | total_sales
---------|---------|-------|-------------
January  | Apple   | 100   | 350
January  | Banana  | 50    | 350
February | Apple   | 120   | 350
February | Banana  | 80    | 350
```

**Calculation:** 100 + 50 + 120 + 80 = 350 (for all rows)

---

### Example 2: PARTITION BY month (Two Windows)

**Query:**
```sql
SELECT 
    month,
    product,
    sales,
    SUM(sales) OVER(PARTITION BY month) AS total_sales_by_month
FROM sales_data;
```

**How SQL sees it:**
```
┌─────────────────────┐  ┌─────────────────────┐
│ WINDOW 1: January   │  │ WINDOW 2: February  │
│ Apple   | 100       │  │ Apple   | 120       │
│ Banana  | 50        │  │ Banana  | 80        │
└─────────────────────┘  └─────────────────────┘
```

**Output:**
```
month    | product | sales | total_sales_by_month
---------|---------|-------|---------------------
January  | Apple   | 100   | 150
January  | Banana  | 50    | 150
February | Apple   | 120   | 200
February | Banana  | 80    | 200
```

**Calculation:** 
- January window: 100 + 50 = 150
- February window: 120 + 80 = 200

---

## Real Examples with Different Scenarios

### Sample Data for All Examples

```
order_id | order_date | product_id | order_status | sales
---------|------------|------------|--------------|-------
1        | 2024-01-01 | 101        | Delivered    | 10
2        | 2024-01-02 | 101        | Delivered    | 20
3        | 2024-01-03 | 101        | Shipped      | 30
4        | 2024-01-04 | 101        | Shipped      | 80
5        | 2024-01-05 | 102        | Delivered    | 30
6        | 2024-01-06 | 102        | Shipped      | 60
7        | 2024-01-07 | 102        | Shipped      | 15
8        | 2024-01-08 | 104        | Shipped      | 50
9        | 2024-01-09 | 104        | Delivered    | 35
10       | 2024-01-10 | 105        | Shipped      | 60
```

---

## Task 1: Total Sales Across ALL Orders

**Task:** Find total sales with order details

**Query:**
```sql
SELECT 
    order_id,
    order_date,
    sales,
    SUM(sales) OVER() AS total_sales
FROM sales_orders;
```

**Output:**
```
order_id | order_date | sales | total_sales
---------|------------|-------|-------------
1        | 2024-01-01 | 10    | 390
2        | 2024-01-02 | 20    | 390
3        | 2024-01-03 | 30    | 390
4        | 2024-01-04 | 80    | 390
5        | 2024-01-05 | 30    | 390
6        | 2024-01-06 | 60    | 390
7        | 2024-01-07 | 15    | 390
8        | 2024-01-08 | 50    | 390
9        | 2024-01-09 | 35    | 390
10       | 2024-01-10 | 60    | 390
```

**Explanation:** Every row shows the grand total (390) because we didn't partition!

---

## Task 2: Total Sales by Product

**Task:** Find total sales for each product with order details

**Query:**
```sql
SELECT 
    order_id,
    order_date,
    product_id,
    sales,
    SUM(sales) OVER(PARTITION BY product_id) AS total_sales_by_product
FROM sales_orders;
```

**Output:**
```
order_id | order_date | product_id | sales | total_sales_by_product
---------|------------|------------|-------|------------------------
1        | 2024-01-01 | 101        | 10    | 140
2        | 2024-01-02 | 101        | 20    | 140
3        | 2024-01-03 | 101        | 30    | 140
4        | 2024-01-04 | 101        | 80    | 140
5        | 2024-01-05 | 102        | 30    | 105
6        | 2024-01-06 | 102        | 60    | 105
7        | 2024-01-07 | 102        | 15    | 105
8        | 2024-01-08 | 104        | 50    | 85
9        | 2024-01-09 | 104        | 35    | 85
10       | 2024-01-10 | 105        | 60    | 60
```

**Explanation:**
- Product 101: 10 + 20 + 30 + 80 = 140
- Product 102: 30 + 60 + 15 = 105
- Product 104: 50 + 35 = 85
- Product 105: 60 (only one order)

---

## Task 3: Multiple Aggregation Levels

**Task:** Show individual sales, total by product, AND grand total

**Query:**
```sql
SELECT 
    order_id,
    product_id,
    sales,
    SUM(sales) OVER() AS total_sales,
    SUM(sales) OVER(PARTITION BY product_id) AS total_sales_by_product
FROM sales_orders;
```

**Output:**
```
order_id | product_id | sales | total_sales | total_sales_by_product
---------|------------|-------|-------------|------------------------
1        | 101        | 10    | 390         | 140
2        | 101        | 20    | 390         | 140
3        | 101        | 30    | 390         | 140
4        | 101        | 80    | 390         | 140
5        | 102        | 30    | 390         | 105
6        | 102        | 60    | 390         | 105
7        | 102        | 15    | 390         | 105
8        | 104        | 50    | 390         | 85
9        | 104        | 35    | 390         | 85
10       | 105        | 60    | 390         | 60
```

**Amazing!** We see three levels:
1. Individual sales (10, 20, 30...)
2. Product totals (140, 105, 85, 60)
3. Grand total (390)

---

## Task 4: PARTITION BY Multiple Columns

**Task:** Total sales by product AND order status

**Query:**
```sql
SELECT 
    order_id,
    product_id,
    order_status,
    sales,
    SUM(sales) OVER(PARTITION BY product_id, order_status) AS sales_by_product_and_status
FROM sales_orders;
```

**Output:**
```
order_id | product_id | order_status | sales | sales_by_product_and_status
---------|------------|--------------|-------|-----------------------------
1        | 101        | Delivered    | 10    | 30
2        | 101        | Delivered    | 20    | 30
3        | 101        | Shipped      | 30    | 110
4        | 101        | Shipped      | 80    | 110
5        | 102        | Delivered    | 30    | 30
6        | 102        | Shipped      | 60    | 75
7        | 102        | Shipped      | 15    | 75
8        | 104        | Shipped      | 50    | 50
9        | 104        | Delivered    | 35    | 35
10       | 105        | Shipped      | 60    | 60
```

**Explanation:**
- Product 101 + Delivered: 10 + 20 = 30
- Product 101 + Shipped: 30 + 80 = 110
- Product 102 + Delivered: 30
- Product 102 + Shipped: 60 + 15 = 75
- And so on...

---

## PARTITION BY Options Summary

| Option | Syntax | Result |
|--------|--------|--------|
| **No partition** | `OVER()` | 1 window (all data) |
| **One column** | `OVER(PARTITION BY product)` | One window per product |
| **Multiple columns** | `OVER(PARTITION BY product, status)` | One window per combination |

---

## Key Points to Remember 💡

1. **PARTITION BY is optional** - You can skip it!
2. **Empty OVER() = one big window** - Calculates over all rows
3. **PARTITION BY splits data** - Creates separate groups/windows
4. **Similar to GROUP BY** - But keeps all row details!
5. **Can use multiple columns** - Create combinations of groups

---

## Summary 🎓

**PARTITION BY** controls how your data is divided into groups (windows):

- **No PARTITION BY** → All data = 1 window
- **PARTITION BY product** → Separate window for each product
- **PARTITION BY product, status** → Window for each combination

**Remember:** It's like organizing students by grade level, then calculating each grade's average separately! 📊✨
