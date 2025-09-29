# SQL DENSE_RANK Function - Easy Notes

## What is DENSE_RANK?

**DENSE_RANK** is very similar to RANK, but with one big difference: it **doesn't skip any numbers**!

**Key Features:**
- Handles ties (same values = same rank)
- NO gaps in numbering
- Continuous sequence

---

## Real-Life Example: School Rankings

Imagine ranking students by test scores:
- Two students tie for 2nd place (both get rank 2)
- Next student gets rank 3 (NOT rank 4!)
- No numbers are skipped

---

## How DENSE_RANK Works

### Example Data (Sales):
```
Sales: 100, 80, 80, 54, 20
```

### Step 1: SQL Sorts the Data
```
100  ← Highest
80   ← Tie!
80   ← Tie!
54
20   ← Lowest
```

### Step 2: Assign Ranks (with ties but NO gaps)
```
Sales | Dense Rank
------|------------
100   | 1
80    | 2  ← Both get rank 2
80    | 2  ← Same rank!
54    | 3  ← No skip! Next number is 3
20    | 4  ← Continues: 4
```

**Notice:** All numbers present! 1, 2, 3, 4 - no missing numbers!

---

## DENSE_RANK vs RANK: The Key Difference

### Same Data, Different Results:

| Sales | RANK | DENSE_RANK | Difference |
|-------|------|------------|------------|
| 100 | 1 | 1 | Same |
| 80 | 2 | 2 | Same |
| 80 | 2 | 2 | Same (tied) |
| 54 | 4 | 3 | RANK skips, DENSE_RANK doesn't! |
| 20 | 5 | 4 | Different numbers |

**The Difference:**
- **RANK:** 1, 2, 2, 4, 5 (skips 3)
- **DENSE_RANK:** 1, 2, 2, 3, 4 (no skip!)

---

## Visual Understanding

### How DENSE_RANK Handles Ties:

```
Sales:        100, 80,  80,  54,  20
                   (TIED!)
                    ↓    ↓
Dense Rank:    1,   2,   2,   3,   4
                             ↑
                    NO gap! Goes to 3
```

### Comparison with All Three Functions:

```
Sales: 90, 60, 60, 60, 40, 20, 20, 20, 15, 10

ROW_NUMBER:  1,  2,  3,  4,  5,  6,  7,  8,  9, 10
RANK:        1,  2,  2,  2,  5,  6,  6,  6,  9, 10
DENSE_RANK:  1,  2,  2,  2,  3,  4,  4,  4,  5,  6
                        ↑               ↑
                   No gaps!        No gaps!
```

---

## Two Important Rules of DENSE_RANK

### Rule 1: Handles Ties
✅ Same values share the same rank
✅ Just like RANK function
✅ Fair to all tied values

### Rule 2: NO Gaps (Dense = Packed Tight)
✅ Never skips numbers
✅ Continuous sequence
✅ Next rank is always previous + 1

---

## Basic SQL Syntax

```sql
DENSE_RANK() OVER (ORDER BY column DESC)
```

**Parts:**
- `DENSE_RANK()` = Function name (MUST be empty!)
- `OVER` = Start window definition
- `ORDER BY column` = How to sort (REQUIRED!)
- `DESC` = Descending (highest first)

---

## Complete Example: Ranking Orders by Sales

### Task:
"Rank the orders based on their sales from highest to lowest using DENSE_RANK"

### SQL Query:
```sql
SELECT 
    order_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (ORDER BY sales DESC) AS sales_rank_row,
    RANK() OVER (ORDER BY sales DESC) AS sales_rank,
    DENSE_RANK() OVER (ORDER BY sales DESC) AS sales_rank_dense
FROM sales_orders;
```

### Results:

| Order ID | Sales | Row Number | Rank | Dense Rank |
|----------|-------|------------|------|------------|
| 8 | 90 | 1 | 1 | 1 |
| 7 | 60 | 2 | 2 | 2 |
| 5 | 60 | 3 | 2 | 2 |
| 10 | 60 | 4 | 2 | 2 |
| 6 | 40 | 5 | 5 | 3 |
| 4 | 20 | 6 | 6 | 4 |
| 2 | 20 | 7 | 6 | 4 |
| 9 | 20 | 8 | 6 | 4 |
| 3 | 15 | 9 | 9 | 5 |
| 1 | 10 | 10 | 10 | 6 |

### Observations:

**After first tie (three 60s):**
- Row Number: Goes to 5
- Rank: Skips to 5 (skips 3, 4)
- Dense Rank: Goes to 3 (no skip!)

**After second tie (three 20s):**
- Row Number: Goes to 9
- Rank: Skips to 9 (skips 7, 8)
- Dense Rank: Goes to 5 (no skip!)

**Final ranks:**
- Row Number: 1 to 10 (10 total)
- Rank: 1 to 10 (10 total, with gaps)
- Dense Rank: 1 to 6 (only 6! No gaps)

---

## Understanding the Scale

### Different Ending Numbers:

```
ROW_NUMBER:  1 → 10  (counts all rows)
RANK:        1 → 10  (counts positions, with gaps)
DENSE_RANK:  1 → 6   (counts unique values only)
```

**Why 6 in DENSE_RANK?**
Because there are only **6 different sales values**:
1. 90
2. 60
3. 40
4. 20
5. 15
6. 10

DENSE_RANK assigns one rank per unique value!

---

## Complete Comparison Table

| Sales | Position | ROW_NUMBER | RANK | DENSE_RANK |
|-------|----------|------------|------|------------|
| 90 | 1st | 1 | 1 | 1 |
| 60 | 2nd | 2 | 2 | 2 |
| 60 | 3rd | 3 | 2 | 2 |
| 60 | 4th | 4 | 2 | 2 |
| 40 | 5th | 5 | 5 | 3 |
| 20 | 6th | 6 | 6 | 4 |
| 20 | 7th | 7 | 6 | 4 |
| 20 | 8th | 8 | 6 | 4 |
| 15 | 9th | 9 | 9 | 5 |
| 10 | 10th | 10 | 10 | 6 |

**Summary:**
- **ROW_NUMBER:** All unique, no ties, no gaps
- **RANK:** Ties shared, has gaps
- **DENSE_RANK:** Ties shared, NO gaps

---

## When to Use DENSE_RANK?

### Use DENSE_RANK When:
✅ You want ties to share ranks
✅ You DON'T want gaps in numbering
✅ You want continuous sequence
✅ Number of unique values matters
✅ Rankings should be "dense" (packed tight)

### Don't Use DENSE_RANK When:
❌ You need unique numbers for each row (use ROW_NUMBER)
❌ You want to show true position with gaps (use RANK)

---

## Using with PARTITION BY

Rank within each group (no gaps per group):

```sql
SELECT 
    order_id,
    product_id,
    sales,
    DENSE_RANK() OVER (
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS dense_rank_within_product
FROM sales_orders;
```

Each product group ranks independently with no gaps!

---

## Common Use Cases

### 1. Level/Grade Assignments
"Assign grades A, B, C (no gaps between grades)"

### 2. Tier Rankings
"Platinum, Gold, Silver, Bronze (consecutive tiers)"

### 3. Category Rankings
"Rank products by category (continuous numbering)"

### 4. Skill Levels
"Beginner (1), Intermediate (2), Advanced (3)"

---

## Why "DENSE"?

**Dense** means "tightly packed" with no empty spaces!

Think of it like books on a shelf:
- **RANK:** Books with gaps between them
- **DENSE_RANK:** Books pushed together, no gaps

---

## Step-by-Step Example

### Data: 100, 80, 80, 54, 20

**Using RANK:**
- 100 → Rank 1
- 80 → Rank 2 (tied)
- 80 → Rank 2 (tied)
- 54 → Rank 4 (skip 3!)
- 20 → Rank 5

**Using DENSE_RANK:**
- 100 → Rank 1
- 80 → Rank 2 (tied)
- 80 → Rank 2 (tied)
- 54 → Rank 3 (no skip!)
- 20 → Rank 4

**Result:**
- RANK: 1, 2, 2, 4, 5 (gap at 3)
- DENSE_RANK: 1, 2, 2, 3, 4 (no gap!)

---

## Key Formulas

### Final Rank with ROW_NUMBER:
**Always equals total number of rows**

### Final Rank with RANK:
**Always equals total number of rows**

### Final Rank with DENSE_RANK:
**Equals number of unique values**

Example with 10 rows and 6 unique values:
- ROW_NUMBER ends at: 10
- RANK ends at: 10
- DENSE_RANK ends at: 6

---

## Remember These Rules!

1. **Function is EMPTY:** `DENSE_RANK()` not `DENSE_RANK(sales)`
2. **ORDER BY is REQUIRED:** Must sort the data
3. **PARTITION BY is OPTIONAL:** Use to rank within groups
4. **Handles TIES:** Same values = same rank
5. **NO GAPS:** Never skips numbers
6. **Continuous sequence:** 1, 2, 3, 4... (no missing)
7. **Ends at unique values:** Not total rows

---

## Practice Questions

1. What's the main difference between RANK and DENSE_RANK?
2. Can DENSE_RANK skip numbers?
3. If three rows share rank 5, what's the next DENSE_RANK?
4. Does DENSE_RANK handle ties?
5. If you have 100 rows but only 10 unique values, what's the highest DENSE_RANK?
6. When would you use DENSE_RANK instead of RANK?

### Answers:
3. Next rank is **6** (no skip!)
5. Highest DENSE_RANK is **10** (number of unique values)

---

## Quick Decision Guide

**Need unique numbers?** → ROW_NUMBER
**Need true positions with gaps?** → RANK
**Need continuous numbering with ties?** → DENSE_RANK

---

## Key Takeaway

**DENSE_RANK is like numbering floors in a building - even if multiple apartments share a floor (tie), you still number the next floor consecutively. Floor 1, Floor 2, Floor 3... no skipping floors!**

Perfect when you want fair ties AND continuous numbering!
