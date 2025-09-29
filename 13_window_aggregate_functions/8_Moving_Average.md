# SQL Moving Average & Rolling Average - Easy Notes

## What is Moving Average?

**Moving Average** is like running total, but instead of adding numbers, we **find the average** as we go!

### Real-Life Example:
Imagine tracking your test scores over time:
- Test 1: 80 → Average = 80
- Test 2: 90 → Average = (80 + 90) ÷ 2 = 85
- Test 3: 70 → Average = (80 + 90 + 70) ÷ 3 = 80
- Test 4: 100 → Average = (80 + 90 + 70 + 100) ÷ 4 = 85

**See?** The average keeps changing as you add more scores!

---

## Moving Average vs Rolling Average

| Moving Average | Rolling Average |
|----------------|-----------------|
| Uses ALL data from start | Uses FIXED window (like last 2 values) |
| Keeps growing | Window slides forward |
| Example: All test scores | Example: Last 2 test scores |

---

## Task 1: Calculate Moving Average for Each Product

**Question:** Find the moving average of sales for each product over time.

### Step-by-Step Solution:

**Step 1:** Get the basic data
```sql
SELECT 
    order_id,
    product_id,
    order_date,
    sales
FROM sales_orders;
```

**Step 2:** Add simple average (without ORDER BY)
```sql
SELECT 
    order_id,
    product_id,
    order_date,
    sales,
    AVG(sales) OVER (PARTITION BY product_id) AS average_by_product
FROM sales_orders;
```

**Problem:** This gives only ONE average for each product (not moving!)

**Step 3:** Add ORDER BY to create MOVING effect
```sql
SELECT 
    order_id,
    product_id,
    order_date,
    sales,
    AVG(sales) OVER (
        PARTITION BY product_id 
        ORDER BY order_date
    ) AS moving_average
FROM sales_orders;
```

**Result:** Now the average changes with each new order! ✨

---

## How Moving Average Works

### Example Data (Product 101):

| Order Date | Sales | Moving Average | How It's Calculated |
|------------|-------|----------------|---------------------|
| Jan 1 | 10 | 10 | 10 ÷ 1 = 10 |
| Jan 5 | 20 | 15 | (10 + 20) ÷ 2 = 15 |
| Jan 10 | 19 | 16.33 | (10 + 20 + 19) ÷ 3 = 16.33 |
| Jan 15 | 90 | 35 | (10 + 20 + 19 + 90) ÷ 4 = 35 |

**Key Point:** Each new row includes ALL previous values in the calculation!

---

## Task 2: Rolling Average (Fixed Window)

**Question:** Calculate moving average for each product, but include **only the current order and next order** (2 orders total).

### SQL Code:
```sql
SELECT 
    order_id,
    product_id,
    order_date,
    sales,
    AVG(sales) OVER (
        PARTITION BY product_id 
        ORDER BY order_date
        ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING
    ) AS rolling_average
FROM sales_orders;
```

**What's different?**
- `ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING` = Only 2 rows at a time!
- "CURRENT ROW" = this row
- "1 FOLLOWING" = next row

---

## How Rolling Average Works

### Example Data:

| Order | Sales | Rolling Avg | Calculation |
|-------|-------|-------------|-------------|
| 1 | 10 | 15 | (10 + 20) ÷ 2 = 15 |
| 2 | 20 | 19.5 | (20 + 19) ÷ 2 = 19.5 |
| 3 | 19 | 54.5 | (19 + 90) ÷ 2 = 54.5 |
| 4 | 90 | 90 | Only 90 (no next row) |

**See the difference?**
- Each calculation uses only **2 numbers** (current + next)
- The window **slides** forward with each row
- Last row has no "next," so it stays as is

---

## Visual Understanding

### Moving Average (Growing):
```
Row 1: [10] → 10
Row 2: [10, 20] → 15
Row 3: [10, 20, 19] → 16.33
Row 4: [10, 20, 19, 90] → 35  ← Keeps growing!
```

### Rolling Average (Fixed - 2 values):
```
Row 1: [10, 20] → 15
Row 2: [20, 19] → 19.5        ← Dropped 10!
Row 3: [19, 90] → 54.5        ← Dropped 20!
Row 4: [90] → 90              ← No next value
```

---

## Complete Use Cases Summary

### 1. Overall Total (No Window Definition)
```sql
SUM(sales) OVER ()
```
**What it does:** Adds ALL sales in entire table
**Use for:** Grand totals

### 2. Total Per Group (PARTITION BY only)
```sql
SUM(sales) OVER (PARTITION BY product_id)
```
**What it does:** Adds sales for EACH product separately
**Use for:** Comparing different categories

### 3. Running Total (PARTITION BY + ORDER BY)
```sql
SUM(sales) OVER (PARTITION BY product_id ORDER BY order_date)
```
**What it does:** Cumulative sum from start to current row
**Use for:** Tracking progress over time

### 4. Rolling Total (PARTITION BY + ORDER BY + Frame)
```sql
SUM(sales) OVER (
    PARTITION BY product_id 
    ORDER BY order_date
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
)
```
**What it does:** Sum of fixed window (like last 3 rows)
**Use for:** Recent trends analysis

---

## All These Work with Other Functions Too!

You can replace AVG with:
- **SUM** → Running/Rolling totals
- **COUNT** → Running/Rolling counts
- **MAX** → Running/Rolling maximum
- **MIN** → Running/Rolling minimum

---

## Important Terms to Remember

| Term | Meaning | Example |
|------|---------|---------|
| **PARTITION BY** | Divide into groups | Separate by product |
| **ORDER BY** | Sort data | Sort by date |
| **CURRENT ROW** | This row | The row we're on now |
| **1 FOLLOWING** | Next 1 row | The row after current |
| **2 PRECEDING** | Previous 2 rows | 2 rows before current |
| **ROWS BETWEEN** | Define window size | Set how many rows to include |

---

## Frame Clause Options

```sql
-- Include everything from start
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

-- Include last 3 rows (including current)
ROWS BETWEEN 2 PRECEDING AND CURRENT ROW

-- Include current and next row
ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING

-- Include previous, current, and next row
ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
```

---

## Practice Questions

1. What's the difference between moving average and rolling average?
2. What does "1 FOLLOWING" mean?
3. If you want average of last 3 orders, what frame clause would you use?
4. Why do we use ORDER BY with moving average?
5. What happens if you don't specify a frame clause with ORDER BY?

---

## Key Takeaways

✅ **Moving Average** = Average that grows with each new value
✅ **Rolling Average** = Average of fixed window that slides
✅ **ORDER BY** creates the "moving" effect
✅ **Frame clause** creates the "rolling" effect
✅ Works with all aggregate functions (SUM, COUNT, MAX, MIN)
✅ Super useful for analyzing trends over time!

---

## Quick Comparison Table

| Type | Includes | Window Size | Use Case |
|------|----------|-------------|----------|
| Overall Average | Everything | All rows | Grand average |
| Average by Group | One group | All in group | Compare categories |
| Moving Average | From start to current | Growing | Long-term trend |
| Rolling Average | Fixed window | Fixed (e.g., 2-3 rows) | Recent trend |

**Remember:** Window functions are POWERFUL tools for analyzing data over time! 📊📈
