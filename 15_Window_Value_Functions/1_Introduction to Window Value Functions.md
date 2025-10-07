# SQL Window Value Functions: Introduction
## A Simple Guide for 7th Grade Students

## What Are Value Functions (Analytic Functions)? 🔍

**Value functions** let you access data from OTHER rows while staying on your current row!

**Think of it like this:**
- You're looking at your March test score
- You want to compare it with your February score (previous month)
- OR compare it with your April score (next month)
- Value functions let you see ALL of them in one row!

---

## The Four Value Functions

| Function | What It Does | Example |
|----------|-------------|---------|
| **LAG()** | Gets value from PREVIOUS row | Last month's sales |
| **LEAD()** | Gets value from NEXT row | Next month's sales |
| **FIRST_VALUE()** | Gets value from FIRST row | January's sales |
| **LAST_VALUE()** | Gets value from LAST row | December's sales |

---

## Visual Example: How They Work

### Sample Data (Current Processing: March)
```
month    | sales
---------|-------
January  | 20    ← FIRST_VALUE
February | 10    ← LAG (previous)
March    | 15    ← CURRENT ROW (you are here!)
April    | 5     ← LEAD (next)
May      | 25
July     | 40    ← LAST_VALUE
```

### What Each Function Returns (when at March):
- **Current sales:** 15
- **LAG()** (February): 10
- **LEAD()** (April): 5
- **FIRST_VALUE()** (January): 20
- **LAST_VALUE()** (July): 40

---

## Function Syntax and Rules

### LAG() and LEAD()
```sql
LAG(expression, offset, default)
LEAD(expression, offset, default)
```

**Parameters:**
- **expression** - Which column to get (required)
- **offset** - How many rows back/forward (optional, default = 1)
- **default** - What to show if no row exists (optional)

### FIRST_VALUE() and LAST_VALUE()
```sql
FIRST_VALUE(expression)
LAST_VALUE(expression)
```

**Parameters:**
- **expression** - Which column to get (required)

---

## Important Rules for Value Functions ⚠️

| Rule | LAG/LEAD | FIRST_VALUE/LAST_VALUE |
|------|----------|------------------------|
| **PARTITION BY** | Optional | Optional |
| **ORDER BY** | ✅ REQUIRED | ✅ REQUIRED |
| **FRAME clause** | ❌ NOT allowed | ✅ Optional (recommended for LAST_VALUE) |
| **Data types** | Any | Any |

**Remember:** ORDER BY is REQUIRED for all value functions!

---

## Example 1: LAG() - Compare with Previous Month

### Sample Data
```
month    | sales
---------|-------
January  | 20
February | 10
March    | 15
April    | 5
May      | 25
```

### Query
```sql
SELECT 
    month,
    sales,
    LAG(sales) OVER(ORDER BY month) AS previous_month_sales
FROM monthly_sales;
```

### Output
```
month    | sales | previous_month_sales
---------|-------|---------------------
January  | 20    | NULL
February | 10    | 20
March    | 15    | 10
April    | 5     | 15
May      | 25    | 5
```

**Explanation:**
- January: No previous month → NULL
- February: Previous = January (20)
- March: Previous = February (10)
- April: Previous = March (15)
- May: Previous = April (5)

---

## Example 2: LEAD() - Compare with Next Month

### Query
```sql
SELECT 
    month,
    sales,
    LEAD(sales) OVER(ORDER BY month) AS next_month_sales
FROM monthly_sales;
```

### Output
```
month    | sales | next_month_sales
---------|-------|------------------
January  | 20    | 10
February | 10    | 15
March    | 15    | 5
April    | 5     | 25
May      | 25    | NULL
```

**Explanation:**
- January: Next = February (10)
- February: Next = March (15)
- March: Next = April (5)
- April: Next = May (25)
- May: No next month → NULL

---

## Example 3: LAG() with Offset (Look Back 2 Rows)

### Query
```sql
SELECT 
    month,
    sales,
    LAG(sales, 2) OVER(ORDER BY month) AS two_months_ago
FROM monthly_sales;
```

### Output
```
month    | sales | two_months_ago
---------|-------|----------------
January  | 20    | NULL
February | 10    | NULL
March    | 15    | 20
April    | 5     | 10
May      | 25    | 15
```

**Explanation:**
- January & February: Not enough previous rows → NULL
- March: 2 months ago = January (20)
- April: 2 months ago = February (10)
- May: 2 months ago = March (15)

---

## Example 4: LAG() with Default Value

### Query
```sql
SELECT 
    month,
    sales,
    LAG(sales, 1, 0) OVER(ORDER BY month) AS previous_month_sales
FROM monthly_sales;
```

### Output
```
month    | sales | previous_month_sales
---------|-------|---------------------
January  | 20    | 0
February | 10    | 20
March    | 15    | 10
April    | 5     | 15
May      | 25    | 5
```

**Explanation:** January shows 0 instead of NULL (we set default = 0)

---

## Example 5: FIRST_VALUE() - Compare with First Month

### Query
```sql
SELECT 
    month,
    sales,
    FIRST_VALUE(sales) OVER(ORDER BY month) AS first_month_sales
FROM monthly_sales;
```

### Output
```
month    | sales | first_month_sales
---------|-------|-------------------
January  | 20    | 20
February | 10    | 20
March    | 15    | 20
April    | 5     | 20
May      | 25    | 20
```

**Explanation:** Every row shows January's sales (20) - the first value!

---

## Example 6: LAST_VALUE() - Compare with Last Month

### Query (with proper frame)
```sql
SELECT 
    month,
    sales,
    LAST_VALUE(sales) OVER(
        ORDER BY month
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_month_sales
FROM monthly_sales;
```

### Output
```
month    | sales | last_month_sales
---------|-------|------------------
January  | 20    | 25
February | 10    | 25
March    | 15    | 25
April    | 5     | 25
May      | 25    | 25
```

**Explanation:** Every row shows May's sales (25) - the last value!

**Important:** LAST_VALUE() needs a frame definition to work correctly!

---

## Practical Example: Sales Comparison

### Sample Data
```
month    | sales
---------|-------
January  | 100
February | 120
March    | 90
April    | 110
May      | 130
```

### Query: Complete Comparison
```sql
SELECT 
    month,
    sales AS current_sales,
    LAG(sales) OVER(ORDER BY month) AS previous_month,
    LEAD(sales) OVER(ORDER BY month) AS next_month,
    sales - LAG(sales) OVER(ORDER BY month) AS change_from_previous,
    FIRST_VALUE(sales) OVER(ORDER BY month) AS first_month,
    LAST_VALUE(sales) OVER(
        ORDER BY month
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_month
FROM monthly_sales;
```

### Output
```
month    | current_sales | previous_month | next_month | change_from_previous | first_month | last_month
---------|---------------|----------------|------------|----------------------|-------------|------------
January  | 100           | NULL           | 120        | NULL                 | 100         | 130
February | 120           | 100            | 90         | 20                   | 100         | 130
March    | 90            | 120            | 110        | -30                  | 100         | 130
April    | 110           | 90             | 130        | 20                   | 100         | 130
May      | 130           | 110            | NULL       | 20                   | 100         | 130
```

**Amazing insights!**
- See previous and next month side by side
- Calculate change from previous month
- Compare with first and last months
- All in one query!

---

## Summary Table 📊

| Function | Direction | When to Use |
|----------|-----------|-------------|
| **LAG()** | Look backwards | Compare with previous period |
| **LEAD()** | Look forwards | Compare with next period |
| **FIRST_VALUE()** | Look at start | Compare with beginning |
| **LAST_VALUE()** | Look at end | Compare with final value |

---

## Key Points to Remember 💡

1. **ORDER BY is REQUIRED** - Must sort data first!
2. **LAG = Previous**, **LEAD = Next**
3. **FIRST_VALUE = First row**, **LAST_VALUE = Last row**
4. **NULL appears** when no row exists
5. **Use default values** to replace NULL
6. **LAST_VALUE needs frame** for correct results
7. **Any data type allowed** - numbers, text, dates

---

## Common Uses 🎯

- **Track trends:** Compare sales month-over-month
- **Calculate changes:** Current vs previous
- **Find growth:** This year vs last year
- **Analyze patterns:** Weekly comparisons
- **Benchmark:** Compare to first/best performance

**Remember:** Value functions are like time travel for your data - you can see past, present, and future all at once! ⏰✨
