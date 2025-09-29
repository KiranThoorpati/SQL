# SQL RANK Function - Easy Notes

## What is RANK?

**RANK** gives each row a number, but this time it **handles ties** - rows with the same value share the same rank!

**Key Feature:** When there's a tie, it **skips numbers** to show the true position.

---

## Real-Life Example: Olympics Medals

Think about Olympic medals:
- Two athletes tie for 1st place (both get GOLD)
- There's NO silver medal
- Next athlete gets BRONZE (3rd place)

**See?** We skip 2nd place because two people shared 1st!

---

## How RANK Works

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

### Step 2: Assign Ranks (with ties and gaps)
```
Sales | Rank
------|------
100   | 1
80    | 2  ← Both get rank 2
80    | 2  ← Same rank!
54    | 4  ← Skipped 3!
20    | 5
```

**Notice:** No rank 3! It was skipped because two rows shared rank 2.

---

## Two Important Rules of RANK

### Rule 1: Handles Ties (Same Values = Same Rank)
✅ Rows with same value share the same rank
✅ They get identical numbers
✅ Example: Two rows with 80 both get rank 2

### Rule 2: Leaves Gaps (Skips Numbers)
✅ After a tie, it skips ranks
✅ Gap size = number of tied rows
✅ Example: After two rank 2s, next is rank 4 (not 3!)

---

## Visual Understanding

### How RANK Handles Ties:

```
Position in list: 1st, 2nd, 3rd, 4th, 5th
                   ↓    ↓    ↓    ↓    ↓
Sales:            100, 80,  80,  54,  20
                       (TIED!)
                        ↓    ↓
Rank:              1,   2,   2,   4,   5
                             ↑
                        Skipped 3!
```

### Why Skip 3?
Because there are **2 people** in 2nd place, the **3rd person** is actually in **4th position**!

---

## RANK vs ROW_NUMBER

### Same Data, Different Results:

| Sales | ROW_NUMBER | RANK | Difference |
|-------|------------|------|------------|
| 90 | 1 | 1 | Same |
| 60 | 2 | 2 | Same |
| 60 | 3 | 2 | RANK shares! |
| 60 | 4 | 2 | RANK shares! |
| 40 | 5 | 5 | RANK skipped 3 & 4! |
| 20 | 6 | 6 | Same |
| 20 | 7 | 6 | RANK shares! |
| 20 | 8 | 6 | RANK shares! |
| 15 | 9 | 9 | RANK skipped 7 & 8! |
| 10 | 10 | 10 | Same |

**Key Differences:**
- **ROW_NUMBER:** Always unique (2, 3, 4)
- **RANK:** Shares ranks, skips numbers (2, 2, 2, 5)

---

## Basic SQL Syntax

```sql
RANK() OVER (ORDER BY column DESC)
```

**Parts:**
- `RANK()` = Function name (MUST be empty!)
- `OVER` = Start window definition
- `ORDER BY column` = How to sort (REQUIRED!)
- `DESC` = Descending (highest first)

---

## Complete Example: Ranking Orders by Sales

### Task:
"Rank the orders based on their sales from highest to lowest using RANK"

### SQL Query:
```sql
SELECT 
    order_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (ORDER BY sales DESC) AS sales_rank_row,
    RANK() OVER (ORDER BY sales DESC) AS sales_rank
FROM sales_orders;
```

### Results:

| Order ID | Product ID | Sales | Rank Row | Rank |
|----------|------------|-------|----------|------|
| 8 | 101 | 90 | 1 | 1 |
| 7 | 102 | 60 | 2 | 2 |
| 5 | 101 | 60 | 3 | 2 |
| 10 | 103 | 60 | 4 | 2 |
| 6 | 102 | 40 | 5 | 5 |
| 4 | 101 | 20 | 6 | 6 |
| 2 | 101 | 20 | 7 | 6 |
| 9 | 103 | 20 | 8 | 6 |
| 3 | 102 | 15 | 9 | 9 |
| 1 | 101 | 10 | 10 | 10 |

### Observations:

**Three orders with sales = 60:**
- Row Number: 2, 3, 4 (all different)
- Rank: 2, 2, 2 (all same!)
- Next rank: 5 (skipped 3 and 4)

**Three orders with sales = 20:**
- Row Number: 6, 7, 8 (all different)
- Rank: 6, 6, 6 (all same!)
- Next rank: 9 (skipped 7 and 8)

---

## Understanding the Gaps

### Why Does RANK Skip Numbers?

Think of it as **true positions**:

```
Position 1: One person (rank 1)
Position 2: Three people tied (all rank 2)
Position 3: (occupied by tied person)
Position 4: (occupied by tied person)
Position 5: Next person (rank 5)
```

The next person is **truly** in 5th position, not 3rd!

---

## Step-by-Step Example

### Data: 100, 80, 80, 54, 20

**Step 1:** Sort (already sorted)

**Step 2:** Assign ranks
- 100 → Rank 1 (1st position)
- 80 → Rank 2 (2nd position, tied with next)
- 80 → Rank 2 (still 2nd position, tied)
- 54 → Rank 4 (actually 4th position! Two people before)
- 20 → Rank 5 (5th position)

**Final Result:** 1, 2, 2, 4, 5 (no rank 3!)

---

## Using with PARTITION BY

Rank within each group separately:

### Example: Rank orders within each product

```sql
SELECT 
    order_id,
    product_id,
    sales,
    RANK() OVER (
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS rank_within_product
FROM sales_orders;
```

### Results:

| Order ID | Product ID | Sales | Rank Within Product |
|----------|------------|-------|---------------------|
| 8 | 101 | 90 | 1 |
| 5 | 101 | 60 | 2 |
| 4 | 101 | 20 | 3 |
| 1 | 101 | 10 | 4 |
| 7 | 102 | 60 | 1 |
| 6 | 102 | 40 | 2 |
| 3 | 102 | 15 | 3 |
| 10 | 103 | 60 | 1 |
| 9 | 103 | 20 | 2 |

Each product group ranks independently!

---

## When to Use RANK?

### Use RANK When:
✅ You want ties to share the **same rank**
✅ You want to show **true positions**
✅ It's important to show gaps (like Olympics)
✅ You're finding "top performers" where ties matter
✅ You want to be fair with ties

### Don't Use RANK When:
❌ You need unique numbers for every row
❌ You don't want gaps in numbering
❌ Gaps will confuse users

For unique numbers, use ROW_NUMBER!
For no gaps, use DENSE_RANK!

---

## Common Use Cases

### 1. Sports Rankings
"Rank players by score (ties allowed)"

### 2. Academic Rankings
"Rank students by marks (same marks = same rank)"

### 3. Sales Leaderboard
"Top salespeople (fair for ties)"

### 4. Competition Results
"Award positions with proper gaps"

---

## Quick Comparison: Three Ranking Functions

### Same Data: 90, 60, 60, 60, 40, 20

| Function | Results | Characteristics |
|----------|---------|-----------------|
| ROW_NUMBER | 1,2,3,4,5,6 | Unique, no gaps, no ties |
| RANK | 1,2,2,2,5,6 | Shares ranks, has gaps |
| DENSE_RANK | 1,2,2,2,3,4 | Shares ranks, NO gaps |

---

## Remember These Rules!

1. **Function is EMPTY:** `RANK()` not `RANK(sales)`
2. **ORDER BY is REQUIRED:** Must sort the data
3. **PARTITION BY is OPTIONAL:** Use to rank within groups
4. **Handles TIES:** Same values = same rank
5. **Leaves GAPS:** Skips numbers after ties
6. **Shows TRUE POSITION:** Next rank = actual position

---

## The Gap Formula

**Number of ranks skipped = Number of tied rows - 1**

Examples:
- 2 people tied → skip 1 rank
- 3 people tied → skip 2 ranks
- 4 people tied → skip 3 ranks

---

## Practice Questions

1. What does RANK do differently from ROW_NUMBER?
2. Can two rows have the same RANK?
3. If three rows share rank 5, what's the next rank?
4. Does RANK leave gaps in numbering?
5. Why does RANK skip numbers?
6. When would you use RANK instead of ROW_NUMBER?
7. What happens to rank 3 if two rows are rank 2?

### Answers to #3 and #7:
- If three rows share rank 5, next rank is **8** (skips 6 and 7)
- If two rows are rank 2, rank 3 is **skipped** (next is rank 4)

---

## Key Takeaway

**RANK is like Olympic medals - if two athletes tie for gold (1st place), there's no silver medal (2nd place). The next athlete gets bronze (3rd place)!**

It's perfect when you want to be **fair with ties** and show **true positions**!
