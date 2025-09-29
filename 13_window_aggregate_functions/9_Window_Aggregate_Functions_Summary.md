# SQL Window Aggregate Functions - Complete Recap Notes

## What Are Window Aggregate Functions? 🪟

Window aggregate functions take a **group of values** and give you **one combined answer** for each row, without losing any details!

**Think of it like this:**
- Regular GROUP BY = Combines rows (you lose details)
- Window Functions = Keep all rows + add extra information (you keep everything!)

---

## Basic Rules & Syntax

### What Data Can You Use?

| Function | Accepts | Example |
|----------|---------|---------|
| SUM, AVG, MAX, MIN | Numbers only | Sales, prices, scores |
| COUNT | Any data type | Names, dates, anything! |

### Window Syntax (Everything is Optional!)

```sql
FUNCTION(column) OVER (
    PARTITION BY column1    -- Optional
    ORDER BY column2        -- Optional
    ROWS BETWEEN ...        -- Optional
)
```

**You can use:**
- Nothing (empty window)
- Just PARTITION BY
- Just ORDER BY
- Everything together
- Any combination you want!

---

## 🎯 All Use Cases Summary

Let's learn all the amazing things you can do with window aggregate functions!

---

### 1️⃣ Overall Analysis (Empty Window)

**What it does:** One big number for everything

```sql
SELECT 
    product,
    sales,
    SUM(sales) OVER () AS total_sales_all
FROM orders;
```

**Example:**
```
Product | Sales | Total Sales All
--------|-------|----------------
Caps    | 100   | 500
Gloves  | 150   | 500
Shoes   | 250   | 500
```

**Use for:** Getting grand totals, overall averages

---

### 2️⃣ Total Per Groups (PARTITION BY)

**What it does:** Calculate totals for each category separately

```sql
SELECT 
    product,
    sales,
    SUM(sales) OVER (PARTITION BY product) AS total_by_product
FROM orders;
```

**Example:**
```
Product | Sales | Total by Product
--------|-------|------------------
Caps    | 100   | 300 (all caps together)
Caps    | 200   | 300
Gloves  | 150   | 200 (all gloves together)
Gloves  | 50    | 200
```

**Use for:** Comparing categories, products, customers

---

### 3️⃣ Part-to-Whole Analysis (Compare to Total)

**What it does:** See what percentage each sale is of the total

```sql
SELECT 
    product,
    sales,
    SUM(sales) OVER () AS total_sales,
    sales * 100.0 / SUM(sales) OVER () AS percentage
FROM orders;
```

**Example:**
```
Product | Sales | Total | Percentage
--------|-------|-------|------------
Caps    | 100   | 500   | 20%
Gloves  | 150   | 500   | 30%
Shoes   | 250   | 500   | 50%
```

**Use for:** Understanding contribution, market share

---

### 4️⃣ Comparison Analysis (Compare to Average/Max/Min)

**What it does:** Compare each value to average or extremes

```sql
SELECT 
    product,
    sales,
    AVG(sales) OVER () AS average_sales,
    sales - AVG(sales) OVER () AS difference_from_avg,
    MAX(sales) OVER () AS highest_sales,
    MIN(sales) OVER () AS lowest_sales
FROM orders;
```

**Example:**
```
Product | Sales | Avg | Diff from Avg | Max | Min
--------|-------|-----|---------------|-----|----
Caps    | 100   | 166 | -66          | 250 | 100
Gloves  | 150   | 166 | -16          | 250 | 100
Shoes   | 250   | 166 | +84          | 250 | 100
```

**Use for:** Finding above/below average performers

---

### 5️⃣ Data Quality Check (Find Duplicates)

**What it does:** Count how many times something appears

```sql
SELECT 
    customer_id,
    order_id,
    COUNT(*) OVER (PARTITION BY customer_id, order_date) AS duplicate_count
FROM orders;
```

**Example:**
```
Customer | Order | Duplicate Count
---------|-------|----------------
101      | A1    | 1 (✓ good)
102      | B1    | 2 (⚠️ duplicate!)
102      | B2    | 2 (⚠️ duplicate!)
```

**Use for:** Finding duplicate records, checking data quality

---

### 6️⃣ Outlier Detection (Find Unusual Values)

**What it does:** Find values that are very different from others

```sql
SELECT 
    product,
    sales,
    AVG(sales) OVER () AS average_sales,
    CASE 
        WHEN sales > AVG(sales) OVER () THEN 'Above Average'
        WHEN sales < AVG(sales) OVER () THEN 'Below Average'
        ELSE 'Average'
    END AS performance
FROM orders;
```

**Example:**
```
Product | Sales | Average | Performance
--------|-------|---------|-------------
Caps    | 100   | 166     | Below Average
Gloves  | 150   | 166     | Below Average
Shoes   | 250   | 166     | Above Average ⭐
```

**Use for:** Finding unusual data, identifying outliers

---

### 7️⃣ Running Total (Progress Over Time)

**What it does:** Keeps adding from start to current

```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER (ORDER BY month) AS running_total
FROM monthly_sales;
```

**Example:**
```
Month | Sales | Running Total
------|-------|---------------
Jan   | 100   | 100
Feb   | 150   | 250 (100+150)
Mar   | 200   | 450 (100+150+200)
Apr   | 100   | 550 (all together)
```

**Use for:** Tracking budget, monitoring progress, cumulative totals

---

### 8️⃣ Rolling Total (Recent Window)

**What it does:** Totals only last few values (sliding window)

```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER (
        ORDER BY month 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS rolling_total_3months
FROM monthly_sales;
```

**Example:**
```
Month | Sales | Rolling Total (3 months)
------|-------|-------------------------
Jan   | 100   | 100
Feb   | 150   | 250 (Jan+Feb)
Mar   | 200   | 450 (Jan+Feb+Mar)
Apr   | 100   | 450 (Feb+Mar+Apr) ← Dropped Jan!
```

**Use for:** Recent trends, last N days/months analysis

---

### 9️⃣ Moving Average (Average Over Time)

**What it does:** Average that changes as you add data

```sql
SELECT 
    month,
    sales,
    AVG(sales) OVER (ORDER BY month) AS moving_average
FROM monthly_sales;
```

**Example:**
```
Month | Sales | Moving Average
------|-------|---------------
Jan   | 100   | 100
Feb   | 150   | 125 (100+150)÷2
Mar   | 200   | 150 (100+150+200)÷3
Apr   | 100   | 137.5 (all 4)÷4
```

**Use for:** Smoothing out trends, seeing patterns

---

## 🎨 Quick Visual Guide

### How Window Changes the Analysis

```
Empty Window → One big total for everything
    ↓
PARTITION BY → Separate totals for each group
    ↓
+ ORDER BY → Running totals (cumulative)
    ↓
+ FRAME → Rolling window (fixed size)
```

---

## 📊 Complete Comparison Table

| Use Case | Window Definition | What You Get | When to Use |
|----------|------------------|--------------|-------------|
| Overall | `OVER ()` | Grand total | Big picture view |
| By Groups | `OVER (PARTITION BY group)` | Total per category | Compare categories |
| Part-to-Whole | `OVER ()` with division | Percentages | Contribution analysis |
| Comparison | `OVER ()` with math | Differences | Performance evaluation |
| Data Quality | `COUNT(*) OVER (PARTITION BY...)` | Duplicate count | Find errors |
| Outliers | With CASE statement | Above/Below labels | Find unusual data |
| Running Total | `OVER (ORDER BY date)` | Cumulative sum | Track progress |
| Rolling Total | `OVER (ORDER BY date ROWS...)` | Recent window sum | Recent trends |
| Moving Avg | `AVG() OVER (ORDER BY date)` | Changing average | Smooth trends |

---

## 💡 Key Points to Remember

1. **Window functions keep all your rows** (unlike GROUP BY)
2. **Everything in OVER() is optional** - mix and match!
3. **SUM, AVG, MAX, MIN** need numbers
4. **COUNT** works with anything
5. **ORDER BY** creates "running" effect
6. **PARTITION BY** divides into groups
7. **Frame clause** creates "rolling" effect
8. **Super powerful for analytics!**

---

## 🎯 When to Use What?

### Need a grand total? → Empty window
### Comparing categories? → PARTITION BY
### Tracking over time? → ORDER BY
### Recent trends only? → Add frame clause
### Finding outliers? → Compare to AVG/MAX/MIN
### Checking data quality? → Use COUNT

---

## Practice Questions

1. What's the difference between GROUP BY and window functions?
2. Which function can use any data type?
3. What creates the "running" effect?
4. What creates the "rolling" effect?
5. Name 3 use cases for window aggregate functions.
6. Is anything required in the OVER() clause?

---

## What's Next? 🚀

After mastering aggregate window functions, you'll learn about **RANKING functions**!

These help you:
- Rank students by scores
- Find top 3 products
- Assign positions
- And much more!

---

## Final Takeaway

**Window Aggregate Functions are like having a calculator that:**
- ✅ Can see groups of data
- ✅ Keeps all details
- ✅ Adds useful summary information
- ✅ Works over time
- ✅ Makes data analysis super easy!

They're one of the most **powerful tools** in SQL for analyzing data! 🎉
