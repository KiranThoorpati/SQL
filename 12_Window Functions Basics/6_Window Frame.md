# SQL Window Functions: FRAME Clause (Window Frame)
## A Simple Guide for 7th Grade Students

## What is a FRAME? 🖼️

A **FRAME** (also called Window Frame) lets you choose exactly which rows to include in your calculation within each window.

**Real-Life Example:**
- You're tracking daily sales
- Instead of calculating total of ALL days, you want:
  - Today + the next 2 days (a 3-day moving total)
  - Or the previous 3 days (looking back)
- The FRAME lets you choose which rows to include!

---

## The Three Levels of Data Selection

```
1. Entire Table (no partition)
   └── 2. Windows (with PARTITION BY)
       └── 3. Frame (subset within window)
```

**Think of it like:**
- **Table** = Your whole school
- **Window** = One grade level
- **Frame** = A specific group of students in that grade

---

## When Do You Need FRAME?

| Need | Solution |
|------|----------|
| All rows in table | Don't use PARTITION BY or FRAME |
| Groups of data | Use PARTITION BY |
| Subset within groups | Use FRAME clause |

---

## Basic FRAME Syntax

```sql
FUNCTION() OVER(
    ORDER BY column
    ROWS BETWEEN lower_boundary AND upper_boundary
)
```

**Important Rules:**
1. ⚠️ Must have ORDER BY to use FRAME
2. ⚠️ Lower boundary must come before upper boundary

---

## Frame Boundary Keywords

### For Lower Boundary (Start):
- `UNBOUNDED PRECEDING` - First row in window
- `n PRECEDING` - n rows before current row
- `CURRENT ROW` - The current row

### For Upper Boundary (End):
- `CURRENT ROW` - The current row
- `n FOLLOWING` - n rows after current row
- `UNBOUNDED FOLLOWING` - Last row in window

---

## Visual Example 1: CURRENT ROW and 2 FOLLOWING

### Sample Data
```
month    | sales
---------|-------
January  | 10
February | 15
March    | 20
April    | 25
June     | 40
```

### Query
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER(
        ORDER BY month
        ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING
    ) AS total_sales
FROM monthly_sales;
```

### How SQL Processes Each Row:

**Row 1 (January):**
```
[CURRENT: January=10, February=15, March=20]
Frame: 10 + 15 + 20 = 45
```

**Row 2 (February):**
```
[CURRENT: February=15, March=20, April=25]
Frame: 15 + 20 + 25 = 60
```

**Row 3 (March):**
```
[CURRENT: March=20, April=25, June=40]
Frame: 20 + 25 + 40 = 85
```

**Row 4 (April):**
```
[CURRENT: April=25, June=40] (only 1 following available)
Frame: 25 + 40 = 65
```

**Row 5 (June):**
```
[CURRENT: June=40] (no following rows)
Frame: 40
```

### Output:
```
month    | sales | total_sales
---------|-------|-------------
January  | 10    | 45
February | 15    | 60
March    | 20    | 85
April    | 25    | 65
June     | 40    | 40
```

---

## Example 2: CURRENT ROW and UNBOUNDED FOLLOWING

### Query
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER(
        ORDER BY month
        ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
    ) AS total_sales
FROM monthly_sales;
```

### How It Works:
- **UNBOUNDED FOLLOWING** = Always goes to the last row
- Frame gets smaller as you move down

**Row 1 (January):**
```
[January=10, February=15, March=20, April=25, June=40]
Frame: All 5 rows = 110
```

**Row 2 (February):**
```
[February=15, March=20, April=25, June=40]
Frame: 4 rows = 100
```

**Row 5 (June):**
```
[June=40] (current is last row)
Frame: 1 row = 40
```

### Output:
```
month    | sales | total_sales
---------|-------|-------------
January  | 10    | 110
February | 15    | 100
March    | 20    | 85
April    | 25    | 65
June     | 40    | 40
```

---

## Example 3: 1 PRECEDING and CURRENT ROW

### Query
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER(
        ORDER BY month
        ROWS BETWEEN 1 PRECEDING AND CURRENT ROW
    ) AS total_sales
FROM monthly_sales;
```

### How It Works:
- Looks at current row + 1 row before

**Row 1 (January):**
```
[January=10] (no preceding row)
Frame: 10
```

**Row 2 (February):**
```
[January=10, February=15]
Frame: 10 + 15 = 25
```

**Row 3 (March):**
```
[February=15, March=20]
Frame: 15 + 20 = 35
```

### Output:
```
month    | sales | total_sales
---------|-------|-------------
January  | 10    | 10
February | 15    | 25
March    | 20    | 35
April    | 25    | 45
June     | 40    | 65
```

---

## Example 4: UNBOUNDED PRECEDING and CURRENT ROW

### Query
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER(
        ORDER BY month
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM monthly_sales;
```

### How It Works:
- **Running Total!** Always starts from first row

**Row 1:** 10
**Row 2:** 10 + 15 = 25
**Row 3:** 10 + 15 + 20 = 45
**Row 4:** 10 + 15 + 20 + 25 = 70
**Row 5:** 10 + 15 + 20 + 25 + 40 = 110

### Output:
```
month    | sales | running_total
---------|-------|---------------
January  | 10    | 10
February | 15    | 25
March    | 20    | 45
April    | 25    | 70
June     | 40    | 110
```

**This is a RUNNING TOTAL!** Very useful! 📈

---

## Example 5: 1 PRECEDING and 1 FOLLOWING

### Query
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER(
        ORDER BY month
        ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
    ) AS total_sales
FROM monthly_sales;
```

### How It Works:
- 3-day moving average: previous + current + next

**Row 3 (March):**
```
[February=15, March=20, April=25]
Frame: 15 + 20 + 25 = 60
```

### Output:
```
month    | sales | total_sales
---------|-------|-------------
January  | 10    | 25
February | 15    | 45
March    | 20    | 60
April    | 25    | 85
June     | 40    | 65
```

---

## Example 6: UNBOUNDED PRECEDING and UNBOUNDED FOLLOWING

### Query
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER(
        ORDER BY month
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS total_sales
FROM monthly_sales;
```

### How It Works:
- Always ALL rows (same as no PARTITION BY, empty OVER)

### Output:
```
month    | sales | total_sales
---------|-------|-------------
January  | 10    | 110
February | 15    | 110
March    | 20    | 110
April    | 25    | 110
June     | 40    | 110
```

Every row shows the grand total!

---

## Shortcuts! ⚡

### Full Form:
```sql
ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
```

### Shortcut (PRECEDING only):
```sql
ROWS 2 PRECEDING
```

**Works ONLY with PRECEDING!**

❌ Cannot use shortcut with FOLLOWING:
```sql
ROWS 2 FOLLOWING  -- ERROR!
```

---

## The Default Frame (Hidden Rule!)

**If you use ORDER BY without specifying a FRAME, SQL uses:**

```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```

### Example:
```sql
-- These three are IDENTICAL:

-- Option 1: No frame specified
SUM(sales) OVER(ORDER BY month)

-- Option 2: Full syntax
SUM(sales) OVER(ORDER BY month 
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)

-- Option 3: Shortcut
SUM(sales) OVER(ORDER BY month 
    ROWS UNBOUNDED PRECEDING)
```

All create a **running total**!

---

## Complete Example with PARTITION BY

### Sample Data:
```
order_id | order_status | order_date | sales
---------|--------------|------------|-------
1        | Delivered    | 2024-01-01 | 10
2        | Delivered    | 2024-01-02 | 20
3        | Delivered    | 2024-01-03 | 25
4        | Shipped      | 2024-01-01 | 30
5        | Shipped      | 2024-01-02 | 40
6        | Shipped      | 2024-01-03 | 50
```

### Query:
```sql
SELECT 
    order_id,
    order_status,
    order_date,
    sales,
    SUM(sales) OVER(
        PARTITION BY order_status
        ORDER BY order_date
        ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING
    ) AS total_sales
FROM orders;
```

### Output:
```
order_id | order_status | order_date | sales | total_sales
---------|--------------|------------|-------|-------------
1        | Delivered    | 2024-01-01 | 10    | 55
2        | Delivered    | 2024-01-02 | 20    | 45
3        | Delivered    | 2024-01-03 | 25    | 25
4        | Shipped      | 2024-01-01 | 30    | 120
5        | Shipped      | 2024-01-02 | 40    | 90
6        | Shipped      | 2024-01-03 | 50    | 50
```

**Key Point:** Frame calculations stay WITHIN each partition (window)!

---

## Summary Table 📊

| Frame Definition | What It Means |
|-----------------|---------------|
| `CURRENT ROW AND 2 FOLLOWING` | Current + next 2 rows |
| `CURRENT ROW AND UNBOUNDED FOLLOWING` | Current to end |
| `1 PRECEDING AND CURRENT ROW` | Previous + current |
| `UNBOUNDED PRECEDING AND CURRENT ROW` | Start to current (running total!) |
| `1 PRECEDING AND 1 FOLLOWING` | Previous + current + next |
| `UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` | All rows |

---

## Key Rules to Remember 🎯

1. ⚠️ **Must use ORDER BY** to use FRAME
2. 📝 **Default frame exists** when using ORDER BY (unbounded preceding to current)
3. 🔒 **Frame stays within partition** - won't cross window boundaries
4. ⚡ **Shortcut works only with PRECEDING**
5. 📈 **UNBOUNDED PRECEDING + CURRENT ROW = Running Total**

**Remember:** A frame is like choosing which friends to include in a group photo - you pick who's in the shot! 📸✨
