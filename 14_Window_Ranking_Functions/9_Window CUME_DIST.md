# SQL CUME_DIST Function - Easy Notes

## Percentage-Based Ranking Functions

We've learned about integer-based functions (ROW_NUMBER, RANK, DENSE_RANK). Now let's learn percentage-based functions!

### Two Methods of Ranking

| Method | Functions | Output |
|--------|-----------|--------|
| **Integer-Based** | ROW_NUMBER, RANK, DENSE_RANK, NTILE | Whole numbers (1, 2, 3...) |
| **Percentage-Based** | CUME_DIST, PERCENT_RANK | Decimals (0.2, 0.5, 1.0) |

---

## What is CUME_DIST?

**CUME_DIST** = **CUME**ulative **DIST**ribution

It calculates what **percentage** of values are at or below each data point.

**Key Features:**
- Gives values between 0 and 1
- Shows distribution of data
- Handles ties fairly
- Helps understand contributions to total

---

## The Simple Formula

```
CUME_DIST = Position of last occurrence / Total number of rows
```

**In simple words:**
"What fraction of the data is at or below this value?"

---

## How CUME_DIST Works - Step by Step

### Example Data (Sales):
```
Sales: 100, 80, 80, 54, 20
```

### Step 1: Sort the Data (Highest to Lowest)
```
Position | Sales
---------|------
1        | 100
2        | 80
3        | 80   ← Tie!
4        | 54
5        | 20
```

Total rows = 5

---

### Step 2: Calculate CUME_DIST for Each Row

**Row 1 (Sales = 100):**
```
Position = 1
Total rows = 5
CUME_DIST = 1 ÷ 5 = 0.2
```
**Meaning:** 20% of data is at or above 100

---

**Row 2 (Sales = 80) - First occurrence:**
```
This is a tie! Find LAST occurrence of 80
Last occurrence = Position 3
CUME_DIST = 3 ÷ 5 = 0.6
```
**Important:** Even though we're at position 2, we use position 3 because that's where 80 last appears!

---

**Row 3 (Sales = 80) - Last occurrence:**
```
Position = 3 (this is the last 80)
CUME_DIST = 3 ÷ 5 = 0.6
```
**Both 80s get the same percentage!** This is how CUME_DIST handles ties.

---

**Row 4 (Sales = 54):**
```
Position = 4
CUME_DIST = 4 ÷ 5 = 0.8
```
**Meaning:** 80% of data is at or above 54

---

**Row 5 (Sales = 20):**
```
Position = 5
CUME_DIST = 5 ÷ 5 = 1.0
```
**Meaning:** 100% of data is at or above 20 (this is the lowest!)

---

### Complete Results:

| Sales | Position | Last Occurrence | CUME_DIST | Percentage |
|-------|----------|-----------------|-----------|------------|
| 100 | 1 | 1 | 0.2 | 20% |
| 80 | 2 | 3 | 0.6 | 60% |
| 80 | 3 | 3 | 0.6 | 60% |
| 54 | 4 | 4 | 0.8 | 80% |
| 20 | 5 | 5 | 1.0 | 100% |

---

## Understanding Ties - The Tricky Part!

**This is the most confusing part of CUME_DIST!**

When there's a tie, CUME_DIST uses the **LAST position** where that value appears, not the current position.

### Visual Example:

```
Position: 1,  2,  3,  4,  5
Sales:    100, 80, 80, 54, 20
                ↓   ↓
            Both use position 3!
```

**Why?**
Because we're asking: "How many values are at or below 80?"
Answer: 3 values (100, 80, 80)
So: 3 ÷ 5 = 0.6

---

## Basic SQL Syntax

```sql
CUME_DIST() OVER (ORDER BY column DESC)
```

**Parts:**
- `CUME_DIST()` = Function name (MUST be empty!)
- `OVER` = Start window definition
- `ORDER BY column` = How to sort (REQUIRED!)
- `DESC` = Descending (highest first)

---

## Complete Example

### SQL Query:
```sql
SELECT 
    sales,
    CUME_DIST() OVER (ORDER BY sales DESC) AS cumulative_dist
FROM orders;
```

### Results:

| Sales | Cumulative Dist | Meaning |
|-------|-----------------|---------|
| 100 | 0.2 | Top 20% |
| 80 | 0.6 | Top 60% |
| 80 | 0.6 | Top 60% |
| 54 | 0.8 | Top 80% |
| 20 | 1.0 | Bottom (100%) |

---

## What Does the Number Mean?

**CUME_DIST = 0.2 (or 20%)**
- This value is in the **top 20%**
- 20% of all values are at or above this

**CUME_DIST = 0.6 (or 60%)**
- This value is in the **top 60%**
- 60% of all values are at or above this

**CUME_DIST = 1.0 (or 100%)**
- This is the lowest value
- 100% of values are at or above this

---

## Real-Life Example: Class Test Scores

**Scores:** 95, 85, 85, 70, 60

### Calculate CUME_DIST:

| Student | Score | Position | Last Occurrence | CUME_DIST |
|---------|-------|----------|-----------------|-----------|
| A | 95 | 1 | 1 | 1÷5 = 0.2 |
| B | 85 | 2 | 3 | 3÷5 = 0.6 |
| C | 85 | 3 | 3 | 3÷5 = 0.6 |
| D | 70 | 4 | 4 | 4÷5 = 0.8 |
| E | 60 | 5 | 5 | 5÷5 = 1.0 |

**Understanding:**
- Student A: Top 20% (best score!)
- Students B & C: Top 60% (tied)
- Student D: Top 80%
- Student E: Bottom (100%)

---

## The Scale: Always 0 to 1

**Important:** CUME_DIST always gives values between 0 and 1

| Value | Meaning |
|-------|---------|
| Close to 0 | Near the top/best |
| 0.5 | Middle of the pack |
| Close to 1 | Near the bottom/worst |
| Exactly 1.0 | The lowest value |

**Think of it like a percentage slider:**
```
0.0 -------- 0.5 -------- 1.0
Best        Middle      Worst
```

---

## Why Use CUME_DIST?

### Use Case 1: Distribution Analysis
"What percentage of students scored at or below 85?"

Answer: 60% (CUME_DIST = 0.6)

---

### Use Case 2: Percentile Questions
"Is this product in the top 20% of sales?"

If CUME_DIST ≤ 0.2, then YES!

---

### Use Case 3: Performance Comparison
"How does this employee rank compared to all others?"

CUME_DIST = 0.3 means they're in top 30%

---

### Use Case 4: Finding Outliers
"Which values are in the bottom 10%?"

WHERE CUME_DIST > 0.9

---

## Using with PARTITION BY

Calculate distribution within each group:

```sql
SELECT 
    product,
    sales,
    CUME_DIST() OVER (
        PARTITION BY product 
        ORDER BY sales DESC
    ) AS dist_within_product
FROM orders;
```

Each product gets its own distribution (0 to 1)!

---

## Step-by-Step Calculation Practice

### Given: 100, 90, 90, 90, 70, 50

**Calculate CUME_DIST for each:**

1. **100:** Position 1, Last occurrence 1 → 1÷6 = 0.167
2. **90:** Position 2, Last occurrence 4 → 4÷6 = 0.667
3. **90:** Position 3, Last occurrence 4 → 4÷6 = 0.667
4. **90:** Position 4, Last occurrence 4 → 4÷6 = 0.667
5. **70:** Position 5, Last occurrence 5 → 5÷6 = 0.833
6. **50:** Position 6, Last occurrence 6 → 6÷6 = 1.0

**Notice:** All three 90s share 0.667!

---

## Common Mistakes to Avoid

**Mistake 1:** Using current position instead of last occurrence
```
Wrong: Position 2 for first 80 → 2÷5 = 0.4
Right: Position 3 (last 80) → 3÷5 = 0.6
```

**Mistake 2:** Forgetting ORDER BY
```
Wrong: CUME_DIST() OVER ()
Right: CUME_DIST() OVER (ORDER BY sales DESC)
```

**Mistake 3:** Putting values inside function
```
Wrong: CUME_DIST(sales)
Right: CUME_DIST()
```

---

## Key Points to Remember

1. **CUME_DIST gives percentages** (0 to 1)
2. **Formula:** Last occurrence position ÷ Total rows
3. **Handles ties:** Same values get same percentage
4. **Uses last occurrence** for ties (tricky part!)
5. **ORDER BY is required**
6. **Shows distribution** of data points
7. **1.0 is always the lowest** value
8. **Helps answer "what percentage"** questions

---

## Quick Comparison

### CUME_DIST vs Integer Functions:

| Sales | ROW_NUMBER | RANK | DENSE_RANK | CUME_DIST |
|-------|------------|------|------------|-----------|
| 100 | 1 | 1 | 1 | 0.2 |
| 80 | 2 | 2 | 2 | 0.6 |
| 80 | 3 | 2 | 2 | 0.6 |
| 54 | 4 | 4 | 3 | 0.8 |
| 20 | 5 | 5 | 4 | 1.0 |

**See the difference?**
- Integer functions: 1, 2, 3, 4, 5
- CUME_DIST: 0.2, 0.6, 0.6, 0.8, 1.0

---

## Practice Questions

1. What does CUME_DIST stand for?
2. What values can CUME_DIST produce?
3. If CUME_DIST = 0.3, what does it mean?
4. Why do ties get the same CUME_DIST value?
5. What position is used for ties - first or last?
6. If you have 10 rows and a value at position 7, what's the CUME_DIST?
7. What does CUME_DIST = 1.0 mean?

### Answers:
6. 7 ÷ 10 = 0.7 (top 70%)
7. This is the lowest value (100% of data at or above it)

---

## Key Takeaway

**CUME_DIST tells you "what fraction of data is at or below this value" - it's like asking "where does this value sit in the whole distribution?"**

Perfect for understanding data distribution and percentiles!
