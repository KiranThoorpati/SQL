# SQL PERCENT_RANK Function - Easy Notes

## What is PERCENT_RANK?

**PERCENT_RANK** calculates the **relative position** of each row as a percentage.

It's very similar to CUME_DIST, but with one key difference in how it handles ties!

**Key Features:**
- Gives values between 0 and 1
- Shows relative position
- Handles ties fairly
- First value is always 0
- Last value is always 1

---

## The Simple Formula

```
PERCENT_RANK = (Position - 1) / (Total rows - 1)
```

**In simple words:**
"Where does this value rank relative to all others?"

---

## How PERCENT_RANK Works - Step by Step

### Example Data (Sales):
```
Sales: 100, 80, 80, 54, 30
```

### Step 1: Sort the Data (Highest to Lowest)
```
Position | Sales
---------|------
1        | 100
2        | 80
3        | 80   ← Tie!
4        | 54
5        | 30
```

Total rows = 5

---

### Step 2: Calculate PERCENT_RANK for Each Row

**Row 1 (Sales = 100):**
```
Position = 1
PERCENT_RANK = (1 - 1) / (5 - 1)
             = 0 / 4
             = 0
```
**First value is always 0!**

---

**Row 2 (Sales = 80) - First occurrence:**
```
This is a tie! Use FIRST occurrence
Position = 2 (first time we see 80)
PERCENT_RANK = (2 - 1) / (5 - 1)
             = 1 / 4
             = 0.25
```

---

**Row 3 (Sales = 80) - Second occurrence:**
```
Still use position 2 (first occurrence of 80)
PERCENT_RANK = (2 - 1) / (5 - 1)
             = 1 / 4
             = 0.25
```
**Both 80s get the same percentage!**

---

**Row 4 (Sales = 54):**
```
Position = 4
PERCENT_RANK = (4 - 1) / (5 - 1)
             = 3 / 4
             = 0.75
```

---

**Row 5 (Sales = 30):**
```
Position = 5
PERCENT_RANK = (5 - 1) / (5 - 1)
             = 4 / 4
             = 1.0
```
**Last value is always 1.0!**

---

### Complete Results:

| Sales | Position | First Occurrence | PERCENT_RANK | Percentage |
|-------|----------|------------------|--------------|------------|
| 100 | 1 | 1 | 0.0 | 0% |
| 80 | 2 | 2 | 0.25 | 25% |
| 80 | 3 | 2 | 0.25 | 25% |
| 54 | 4 | 4 | 0.75 | 75% |
| 30 | 5 | 5 | 1.0 | 100% |

---

## KEY DIFFERENCE: PERCENT_RANK vs CUME_DIST

### Handling Ties:

**PERCENT_RANK:** Uses **FIRST** occurrence
**CUME_DIST:** Uses **LAST** occurrence

### Visual Example with Ties:

```
Position: 1,  2,  3,  4,  5
Sales:    100, 80, 80, 54, 30
               ↓   ↓
PERCENT_RANK: Both use position 2 (first occurrence)
CUME_DIST:    Both use position 3 (last occurrence)
```

---

## Complete Comparison

### Same Data: 100, 80, 80, 54, 30

| Sales | Position | PERCENT_RANK | CUME_DIST | Difference |
|-------|----------|--------------|-----------|------------|
| 100 | 1 | 0.0 | 0.2 | Different! |
| 80 | 2 | 0.25 | 0.6 | Different! |
| 80 | 3 | 0.25 | 0.6 | Different! |
| 54 | 4 | 0.75 | 0.8 | Different! |
| 30 | 5 | 1.0 | 1.0 | Same |

**Notice:** They give different results!

---

## Understanding the Formulas

### PERCENT_RANK Formula:
```
(Position - 1) / (Total - 1)
```
**Subtracts 1 from both** → More "exclusive"

### CUME_DIST Formula:
```
Position / Total
```
**No subtraction** → More "inclusive"

---

## Inclusive vs Exclusive

**CUME_DIST (Inclusive):**
- Includes current row in calculation
- "How many values at or below this?"

**PERCENT_RANK (Exclusive):**
- Excludes current row somewhat
- "How many values strictly below this?"

---

## Basic SQL Syntax

```sql
PERCENT_RANK() OVER (ORDER BY column DESC)
```

**Parts:**
- `PERCENT_RANK()` = Function name (MUST be empty!)
- `OVER` = Start window definition
- `ORDER BY column` = How to sort (REQUIRED!)
- `DESC` = Descending (highest first)

---

## Real-World Example: Finding Top 40% Products

### Task:
"Find products that fall within the highest 40% of prices"

### Step 1: Get products with percentage ranking
```sql
SELECT 
    product,
    price,
    CUME_DIST() OVER (ORDER BY price DESC) AS dist_rank
FROM sales_products;
```

### Results:
```
Product | Price | Dist Rank
--------|-------|----------
Gloves  | 50    | 0.2  ← Top 20%
Caps    | 45    | 0.4  ← Top 40%
Shoes   | 30    | 0.6
Socks   | 25    | 0.8
Belts   | 15    | 1.0
```

---

### Step 2: Filter to get only top 40%
```sql
SELECT * FROM (
    SELECT 
        product,
        price,
        CUME_DIST() OVER (ORDER BY price DESC) AS dist_rank
    FROM sales_products
)
WHERE dist_rank <= 0.4;
```

### Final Results:
```
Product | Price | Dist Rank
--------|-------|----------
Gloves  | 50    | 0.2
Caps    | 45    | 0.4
```

**Success!** Found products in top 40%

---

### Step 3: Format as percentage (Optional)
```sql
SELECT 
    product,
    price,
    CONCAT(dist_rank * 100, '%') AS dist_rank_percentage
FROM (
    SELECT 
        product,
        price,
        CUME_DIST() OVER (ORDER BY price DESC) AS dist_rank
    FROM sales_products
)
WHERE dist_rank <= 0.4;
```

### Pretty Results:
```
Product | Price | Dist Rank Percentage
--------|-------|---------------------
Gloves  | 50    | 20%
Caps    | 45    | 40%
```

---

## Using PERCENT_RANK Instead

Same task, but with PERCENT_RANK:

```sql
SELECT * FROM (
    SELECT 
        product,
        price,
        PERCENT_RANK() OVER (ORDER BY price DESC) AS pct_rank
    FROM sales_products
)
WHERE pct_rank <= 0.4;
```

### Results (Same!):
```
Product | Price | Pct Rank
--------|-------|----------
Gloves  | 50    | 0.0
Caps    | 45    | 0.25
```

**Both functions work for this use case!**

---

## When to Use Which?

### Use CUME_DIST when:
- You want to know **distribution**
- Question: "What % of data is at or below this?"
- More intuitive for cumulative questions
- Includes current row in count

### Use PERCENT_RANK when:
- You want **relative position**
- Question: "Where does this rank relatively?"
- Better for comparing positions
- Excludes current row somewhat

**Honestly?** They're very similar! Either works for most cases.

---

## Key Similarities

Both functions:
1. Give values between 0 and 1
2. Handle ties (same values get same rank)
3. Require ORDER BY
4. Support PARTITION BY
5. Help with distribution analysis
6. Last value is always 1.0

---

## Key Differences

| Aspect | PERCENT_RANK | CUME_DIST |
|--------|--------------|-----------|
| **First value** | Always 0 | Depends on data |
| **Last value** | Always 1.0 | Always 1.0 |
| **Ties use** | First occurrence | Last occurrence |
| **Formula** | (Position-1)/(Total-1) | Position/Total |
| **Nature** | More exclusive | More inclusive |
| **Focus** | Relative position | Distribution |

---

## Complete Example Comparison

### Data: 95, 85, 85, 70, 60 (5 students' scores)

**PERCENT_RANK:**
```
Student | Score | Position | First Occur | PERCENT_RANK
--------|-------|----------|-------------|-------------
A       | 95    | 1        | 1           | (1-1)/(5-1) = 0.0
B       | 85    | 2        | 2           | (2-1)/(5-1) = 0.25
C       | 85    | 3        | 2           | (2-1)/(5-1) = 0.25
D       | 70    | 4        | 4           | (4-1)/(5-1) = 0.75
E       | 60    | 5        | 5           | (5-1)/(5-1) = 1.0
```

**CUME_DIST:**
```
Student | Score | Position | Last Occur | CUME_DIST
--------|-------|----------|------------|----------
A       | 95    | 1        | 1          | 1/5 = 0.2
B       | 85    | 2        | 3          | 3/5 = 0.6
C       | 85    | 3        | 3          | 3/5 = 0.6
D       | 70    | 4        | 4          | 4/5 = 0.8
E       | 60    | 5        | 5          | 5/5 = 1.0
```

---

## Practice Calculations

### Given: 100, 90, 90, 80, 70, 60 (6 values)

**Calculate PERCENT_RANK:**

1. **100:** (1-1)/(6-1) = 0/5 = **0.0**
2. **90 (first):** (2-1)/(6-1) = 1/5 = **0.2**
3. **90 (second):** (2-1)/(6-1) = 1/5 = **0.2**
4. **80:** (4-1)/(6-1) = 3/5 = **0.6**
5. **70:** (5-1)/(6-1) = 4/5 = **0.8**
6. **60:** (6-1)/(6-1) = 5/5 = **1.0**

---

## Key Points to Remember

1. **PERCENT_RANK gives percentages** (0 to 1)
2. **Formula:** (Position - 1) / (Total - 1)
3. **Handles ties:** Same values get same percentage
4. **Uses FIRST occurrence** for ties
5. **First value is always 0**
6. **Last value is always 1.0**
7. **Very similar to CUME_DIST** but slightly different
8. **ORDER BY is required**

---

## Quick Decision Guide

**Need distribution?** → CUME_DIST
**Need relative position?** → PERCENT_RANK
**Not sure?** → Either works! Pick one and stick with it.

**Pro tip:** CUME_DIST is slightly more popular for distribution analysis.

---

## Practice Questions

1. What's the formula for PERCENT_RANK?
2. What's always the PERCENT_RANK of the first value?
3. For ties, does PERCENT_RANK use first or last occurrence?
4. What's the difference between PERCENT_RANK and CUME_DIST?
5. Can PERCENT_RANK give values outside 0 to 1?
6. If you have 10 rows and a value at position 5, what's PERCENT_RANK?

### Answers:
2. Always 0
3. First occurrence
5. No, always between 0 and 1
6. (5-1)/(10-1) = 4/9 = 0.444

---

## Final Takeaway

**PERCENT_RANK tells you the relative position of a value as a percentage. It's like asking "if I line up all values, where does this one stand in terms of percentage?"**

Very useful for percentile analysis and distribution questions!
