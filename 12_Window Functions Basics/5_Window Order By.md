# SQL Window Functions: ORDER BY
## A Simple Guide for 7th Grade Students

## What is ORDER BY in Window Functions? 📊

**ORDER BY** inside a window function sorts the data within each window (group).

**Real-Life Example:**
- You have test scores from different classes
- You want to rank students from highest to lowest score
- Within each class separately
- ORDER BY helps you sort the scores before ranking!

---

## When Do You NEED ORDER BY?

| Function Type | Is ORDER BY Required? |
|--------------|----------------------|
| **Aggregate** (SUM, AVG, COUNT) | ❌ Optional |
| **Ranking** (RANK, ROW_NUMBER) | ✅ REQUIRED! |
| **Value** (LEAD, LAG, FIRST_VALUE) | ✅ REQUIRED! |

**Why?** You can't rank or access values in order without sorting first!

---

## Basic Syntax

```sql
FUNCTION() OVER(
    PARTITION BY column
    ORDER BY column [ASC/DESC]
)
```

- **ASC** = Ascending (lowest to highest) - This is the default!
- **DESC** = Descending (highest to lowest)

---

## Visual Example: How ORDER BY Works

### Sample Data

```
month    | product | sales
---------|---------|-------
January  | Apple   | 100
January  | Banana  | 50
January  | Orange  | 75
February | Apple   | 70
February | Banana  | 40
February | Orange  | 5
```

---

### Example with PARTITION BY and ORDER BY

**Query:**
```sql
SELECT 
    month,
    product,
    sales,
    RANK() OVER(
        PARTITION BY month 
        ORDER BY sales DESC
    ) AS rank_sales
FROM sales_data;
```

**How SQL processes this:**

**Step 1:** PARTITION BY month - Split into 2 windows
```
┌─────────────────────┐  ┌─────────────────────┐
│ WINDOW 1: January   │  │ WINDOW 2: February  │
│ Apple   | 100       │  │ Apple   | 70        │
│ Banana  | 50        │  │ Banana  | 40        │
│ Orange  | 75        │  │ Orange  | 5         │
└─────────────────────┘  └─────────────────────┘
```

**Step 2:** ORDER BY sales DESC - Sort each window
```
┌─────────────────────┐  ┌─────────────────────┐
│ WINDOW 1: January   │  │ WINDOW 2: February  │
│ Apple   | 100  ← 1st│  │ Apple   | 70   ← 1st│
│ Orange  | 75   ← 2nd│  │ Banana  | 40   ← 2nd│
│ Banana  | 50   ← 3rd│  │ Orange  | 5    ← 3rd│
└─────────────────────┘  └─────────────────────┘
```

**Output:**
```
month    | product | sales | rank_sales
---------|---------|-------|------------
January  | Apple   | 100   | 1
January  | Orange  | 75    | 2
January  | Banana  | 50    | 3
February | Apple   | 70    | 1
February | Banana  | 40    | 2
February | Orange  | 5     | 3
```

**Notice:** Ranking starts over for each month (window)!

---

## Complete Examples

### Sample Data for All Examples

```
order_id | order_date | sales
---------|------------|-------
1        | 2024-01-01 | 50
2        | 2024-01-02 | 90
3        | 2024-01-03 | 30
4        | 2024-01-04 | 70
5        | 2024-01-05 | 10
6        | 2024-01-06 | 80
7        | 2024-01-07 | 40
8        | 2024-01-08 | 60
9        | 2024-01-09 | 20
10       | 2024-01-10 | 85
```

---

## Task 1: Rank Orders from Highest to Lowest

**Query:**
```sql
SELECT 
    order_id,
    order_date,
    sales,
    RANK() OVER(ORDER BY sales DESC) AS rank_sales
FROM sales_orders;
```

**Output:**
```
order_id | order_date | sales | rank_sales
---------|------------|-------|------------
2        | 2024-01-02 | 90    | 1
10       | 2024-01-10 | 85    | 2
6        | 2024-01-06 | 80    | 3
4        | 2024-01-04 | 70    | 4
8        | 2024-01-08 | 60    | 5
1        | 2024-01-01 | 50    | 6
7        | 2024-01-07 | 40    | 7
3        | 2024-01-03 | 30    | 8
9        | 2024-01-09 | 20    | 9
5        | 2024-01-05 | 10    | 10
```

**Explanation:** 
- Highest sales (90) gets rank 1
- Lowest sales (10) gets rank 10
- DESC = highest to lowest

---

## Task 2: Rank Orders from Lowest to Highest

**Query (No DESC = Ascending by default):**
```sql
SELECT 
    order_id,
    order_date,
    sales,
    RANK() OVER(ORDER BY sales) AS rank_sales
FROM sales_orders;
```

**Output:**
```
order_id | order_date | sales | rank_sales
---------|------------|-------|------------
5        | 2024-01-05 | 10    | 1
9        | 2024-01-09 | 20    | 2
3        | 2024-01-03 | 30    | 3
7        | 2024-01-07 | 40    | 4
1        | 2024-01-01 | 50    | 5
8        | 2024-01-08 | 60    | 6
4        | 2024-01-04 | 70    | 7
6        | 2024-01-06 | 80    | 8
10       | 2024-01-10 | 85    | 9
2        | 2024-01-02 | 90    | 10
```

**Explanation:** 
- Lowest sales (10) gets rank 1
- Highest sales (90) gets rank 10
- No DESC or ASC = ascending (default)

---

## Task 3: Rank with Explicit ASC

**Query:**
```sql
SELECT 
    order_id,
    order_date,
    sales,
    RANK() OVER(ORDER BY sales ASC) AS rank_sales
FROM sales_orders;
```

**Output:**
```
order_id | order_date | sales | rank_sales
---------|------------|-------|------------
5        | 2024-01-05 | 10    | 1
9        | 2024-01-09 | 20    | 2
3        | 2024-01-03 | 30    | 3
7        | 2024-01-07 | 40    | 4
1        | 2024-01-01 | 50    | 5
8        | 2024-01-08 | 60    | 6
4        | 2024-01-04 | 70    | 7
6        | 2024-01-06 | 80    | 8
10       | 2024-01-10 | 85    | 9
2        | 2024-01-02 | 90    | 10
```

**Same result as Task 2!** ASC is optional (it's the default).

---

## Task 4: Rank by Product (with PARTITION BY)

**Sample Data:**
```
order_id | product_id | sales
---------|------------|-------
1        | 101        | 50
2        | 101        | 90
3        | 101        | 30
4        | 102        | 70
5        | 102        | 40
6        | 102        | 80
```

**Query:**
```sql
SELECT 
    order_id,
    product_id,
    sales,
    RANK() OVER(
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS rank_within_product
FROM sales_orders;
```

**Output:**
```
order_id | product_id | sales | rank_within_product
---------|------------|-------|--------------------
2        | 101        | 90    | 1
1        | 101        | 50    | 2
3        | 101        | 30    | 3
6        | 102        | 80    | 1
4        | 102        | 70    | 2
5        | 102        | 40    | 3
```

**Explanation:**
- Product 101: Ranked separately (90 is 1st, 50 is 2nd, 30 is 3rd)
- Product 102: Ranked separately (80 is 1st, 70 is 2nd, 40 is 3rd)
- Each product's ranking starts at 1!

---

## ORDER BY with Aggregate Functions (Optional)

**Query:**
```sql
SELECT 
    order_id,
    sales,
    SUM(sales) OVER(ORDER BY order_id) AS running_total
FROM sales_orders;
```

**Sample Output (first 5 rows):**
```
order_id | sales | running_total
---------|-------|---------------
1        | 50    | 50
2        | 90    | 140
3        | 30    | 170
4        | 70    | 240
5        | 10    | 250
```

**Explanation:** Creates a running total! We'll learn more about this later.

---

## Key Differences: ASC vs DESC

| ORDER BY | What It Does | Example |
|----------|-------------|---------|
| `ORDER BY sales` | Lowest → Highest | 10, 20, 30... |
| `ORDER BY sales ASC` | Lowest → Highest | 10, 20, 30... |
| `ORDER BY sales DESC` | Highest → Lowest | 90, 80, 70... |

**Remember:** ASC is the default, so you can skip it!

---

## Common Mistakes to Avoid ❌

### Mistake 1: Forgetting ORDER BY with RANK
```sql
-- ❌ WRONG: RANK needs ORDER BY
RANK() OVER(PARTITION BY product_id)

-- ✅ CORRECT
RANK() OVER(PARTITION BY product_id ORDER BY sales DESC)
```

### Mistake 2: Confusing WHERE and ORDER BY
```sql
-- WHERE filters rows BEFORE the window function
-- ORDER BY (in OVER) sorts WITHIN each window
```

---

## Summary 🎓

**ORDER BY in Window Functions:**
- **Sorts data within each window** (group)
- **Required for**: RANK, ROW_NUMBER, LEAD, LAG
- **Optional for**: SUM, AVG, COUNT, MAX, MIN
- **ASC** = Lowest to highest (default)
- **DESC** = Highest to lowest

**The Complete Pattern:**
```sql
RANK() OVER(
    PARTITION BY column    -- Split into groups
    ORDER BY column DESC   -- Sort within groups
)
```

**Remember:** You can't rank things without sorting them first - just like you can't give out medals without knowing who came 1st, 2nd, and 3rd! 🥇🥈🥉
