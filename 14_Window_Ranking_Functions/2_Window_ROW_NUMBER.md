# SQL ROW_NUMBER Function - Easy Notes

## What is ROW_NUMBER?

**ROW_NUMBER** gives each row a **unique number** (like roll numbers in class), and it **doesn't care about ties**!

**Key Feature:** Even if two rows have the same value, they get different numbers.

---

## Real-Life Example

Imagine numbering students in line:
- 1st person = Number 1
- 2nd person = Number 2
- 3rd person = Number 3
- Even if two students have the same height, they still get different numbers!

---

## How ROW_NUMBER Works

### Example Data (Sales):
```
Sales: 100, 80, 80, 54, 20
```

### Step 1: SQL Sorts the Data (Highest to Lowest)
```
100  ← Highest
80
80   ← Same value!
54
20   ← Lowest
```

### Step 2: Assign Unique Numbers
```
Sales | Row Number
------|------------
100   | 1
80    | 2
80    | 3  ← Different number even though value is same!
54    | 4
20    | 5
```

---

## Three Important Rules of ROW_NUMBER

### Rule 1: Always Unique Numbers
✅ Every row gets a **different** number
✅ No repetitions (no two 1s, no two 2s)
✅ All numbers are distinct

### Rule 2: No Gaps in Sequence
✅ Goes 1, 2, 3, 4, 5...
✅ Never skips (no jumping to 7 or 10)
✅ Continuous sequence

### Rule 3: Doesn't Handle Ties
✅ Two rows with same value = different ranks
✅ Treats every row as unique
✅ Ignores duplicate values

---

## Visual Understanding

### What ROW_NUMBER Does:

```
Same Values ↓
Sales: 100, 80, 80, 54, 20
Rank:   1,  2,  3,  4,  5
            ↑   ↑
    Different ranks even though same sales!
```

### Characteristics:
- **Unique:** 1, 2, 3, 4, 5 (all different)
- **No Gaps:** No missing numbers
- **No Shared Ranks:** Ties get different numbers

---

## Basic SQL Syntax

```sql
ROW_NUMBER() OVER (ORDER BY column DESC)
```

**Parts:**
- `ROW_NUMBER()` = Function name (MUST be empty!)
- `OVER` = Start window definition
- `ORDER BY column` = How to sort (REQUIRED!)
- `DESC` = Descending (highest first)

---

## Complete Example: Ranking Orders by Sales

### Task: 
"Rank the orders based on their sales from highest to lowest"

### SQL Query:
```sql
SELECT 
    order_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (ORDER BY sales DESC) AS sales_rank_row
FROM sales_orders;
```

### Results:

| Order ID | Product ID | Sales | Sales Rank Row |
|----------|------------|-------|----------------|
| 8 | 101 | 90 | 1 |
| 7 | 102 | 60 | 2 |
| 5 | 101 | 60 | 3 |
| 10 | 103 | 60 | 4 |
| 6 | 102 | 40 | 5 |
| 4 | 101 | 20 | 6 |
| 2 | 101 | 20 | 7 |
| 9 | 103 | 20 | 8 |
| 3 | 102 | 15 | 9 |
| 1 | 101 | 10 | 10 |

### Observations:

**Notice the ties:**
- Three orders have sales = 60 (ranks 2, 3, 4)
- Three orders have sales = 20 (ranks 6, 7, 8)

**All get different ranks!** That's how ROW_NUMBER works.

---

## Step-by-Step Breakdown

### What SQL Does:

**Step 1:** Sort by sales (highest to lowest)
```
90 → 60 → 60 → 60 → 40 → 20 → 20 → 20 → 15 → 10
```

**Step 2:** Assign numbers 1, 2, 3, 4, 5...
```
90 = 1
60 = 2
60 = 3  ← Same value, different rank
60 = 4  ← Same value, different rank
40 = 5
20 = 6
20 = 7  ← Same value, different rank
20 = 8  ← Same value, different rank
15 = 9
10 = 10
```

**Step 3:** Show results with original data

---

## Important Points About ORDER BY

### Ascending (ASC) - Lowest First:
```sql
ROW_NUMBER() OVER (ORDER BY sales ASC)
```
Result: 10, 15, 20, 20, 20... (smallest to largest)

### Descending (DESC) - Highest First:
```sql
ROW_NUMBER() OVER (ORDER BY sales DESC)
```
Result: 90, 60, 60, 60... (largest to smallest)

**Most common:** DESC (we usually want top performers first)

---

## Using with PARTITION BY

You can rank **within groups**!

### Example: Rank orders within each product

```sql
SELECT 
    order_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS rank_within_product
FROM sales_orders;
```

### Results:

| Order ID | Product ID | Sales | Rank Within Product |
|----------|------------|-------|---------------------|
| 8 | 101 | 90 | 1 (best for 101) |
| 5 | 101 | 60 | 2 |
| 4 | 101 | 20 | 3 |
| 1 | 101 | 10 | 4 (worst for 101) |
| 7 | 102 | 60 | 1 (best for 102) |
| 6 | 102 | 40 | 2 |
| 3 | 102 | 15 | 3 (worst for 102) |
| 10 | 103 | 60 | 1 (best for 103) |
| 9 | 103 | 20 | 2 (worst for 103) |

**Each product group starts ranking from 1!**

---

## Common Use Cases

### 1. Simple Ranking
"Give me all orders ranked by sales"

### 2. Top N Selection
"Show me the top 5 orders"
```sql
SELECT * FROM (
    SELECT 
        order_id,
        sales,
        ROW_NUMBER() OVER (ORDER BY sales DESC) AS rank
    FROM sales_orders
) WHERE rank <= 5;
```

### 3. Removing Duplicates
"Keep only the first occurrence of each duplicate"

### 4. Pagination
"Show me rows 11-20" (for displaying pages of data)

---

## When to Use ROW_NUMBER?

### Use ROW_NUMBER When:
✅ You need **unique** numbers for every row
✅ You want to select **top N** items
✅ You're removing **duplicates**
✅ You want **consistent** numbering (no gaps)
✅ Ties should get **different** ranks

### Don't Use ROW_NUMBER When:
❌ You want ties to share the **same rank**
❌ You need to know which items are **truly tied**

For those cases, use RANK or DENSE_RANK instead!

---

## Quick Comparison

### ROW_NUMBER vs Other Ranking Functions:

| Sales | ROW_NUMBER | RANK | DENSE_RANK |
|-------|------------|------|------------|
| 90 | 1 | 1 | 1 |
| 60 | 2 | 2 | 2 |
| 60 | 3 | 2 | 2 |
| 60 | 4 | 2 | 2 |
| 40 | 5 | 5 | 3 |
| 20 | 6 | 6 | 4 |

**See?** Only ROW_NUMBER gives completely unique numbers!

---

## Remember These Rules!

1. **Function is EMPTY:** `ROW_NUMBER()` not `ROW_NUMBER(sales)`
2. **ORDER BY is REQUIRED:** Must sort the data
3. **PARTITION BY is OPTIONAL:** Use to rank within groups
4. **Always UNIQUE numbers:** No duplicates ever
5. **No GAPS:** Continuous sequence (1,2,3,4,5...)
6. **Ignores TIES:** Same values get different ranks

---

## Practice Questions

1. What does ROW_NUMBER do?
2. Can two rows have the same ROW_NUMBER?
3. If you have 100 rows, what's the highest ROW_NUMBER?
4. Does ROW_NUMBER handle ties?
5. Is ORDER BY required or optional?
6. Can you use expressions inside ROW_NUMBER()?
7. What happens if two rows have the same sales value?

---

## Key Takeaway

**ROW_NUMBER is like giving every student a unique roll number - even if two students have the same marks, they still get different roll numbers!**

It's the simplest ranking function and perfect when you just need to number your rows uniquely!
