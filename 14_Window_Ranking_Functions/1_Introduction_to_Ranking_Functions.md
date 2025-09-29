# SQL Ranking Functions - Simple Notes

## What is Ranking?

**Ranking** means putting things in order from best to worst (or worst to best) and giving each one a position number!

**Real-Life Example:**
Think of ranking students by test scores:
- 1st place: Student with highest score
- 2nd place: Student with second highest
- 3rd place: Student with third highest
- And so on...

---

## Step 1: Sorting Comes First!

**Before ranking, SQL MUST sort the data!**

Example - Ranking products by sales:

**Unsorted Data:**
```
Product | Sales
--------|------
A       | 10
B       | 30
C       | 20
D       | 10
E       | 70
```

**Sorted Data (Highest to Lowest):**
```
Product | Sales
--------|------
E       | 70    ← First
B       | 30    ← Second
C       | 20    ← Third
A       | 10    ← Fourth
D       | 10    ← Fifth
```

**Now we can rank them!**

---

## Two Methods of Ranking

### Method 1: Integer-Based Ranking (Whole Numbers)

Gives each row a **whole number** (1, 2, 3, 4, 5...)

```
Product | Sales | Rank
--------|-------|------
E       | 70    | 1
B       | 30    | 2
C       | 20    | 3
A       | 10    | 4
D       | 10    | 5
```

**Characteristics:**
- Uses whole numbers (1, 2, 3, 4, 5...)
- Different values each time (not fixed scale)
- Depends on how many rows you have

---

### Method 2: Percentage-Based Ranking (Decimals)

Gives each row a **percentage** between 0 and 1

```
Product | Sales | Percent Rank
--------|-------|-------------
E       | 70    | 0.00 (Top 0%)
B       | 30    | 0.25 (Top 25%)
C       | 20    | 0.50 (Top 50%)
A       | 10    | 0.75 (Top 75%)
D       | 10    | 1.00 (Bottom/100%)
```

**Characteristics:**
- Uses decimals between 0 and 1
- Always same scale (0 to 1)
- Shows position as percentage
- Called "normalized" or "continuous" scale

---

## When to Use Each Method?

### Use Integer-Based Ranking When:

**Question:** "Find the **top 3** products by sales"

You want:
- Specific positions (1st, 2nd, 3rd)
- Top/Bottom N items
- Clear rankings in a list

**Called:** Top-Bottom Analysis

---

### Use Percentage-Based Ranking When:

**Question:** "Find the **top 20%** of products by sales"

You want:
- Understand contributions to total
- See distribution of values
- Compare relative positions

**Called:** Distribution Analysis

---

## All Ranking Functions

### Integer-Based Functions (4 functions):

| Function | What it does |
|----------|-------------|
| **ROW_NUMBER** | Gives unique number to each row |
| **RANK** | Same rank for ties, skips numbers |
| **DENSE_RANK** | Same rank for ties, no gaps |
| **NTILE** | Divides into equal groups |

### Percentage-Based Functions (2 functions):

| Function | What it does |
|----------|-------------|
| **PERCENT_RANK** | Position as percentage (0 to 1) |
| **CUME_DIST** | Cumulative distribution (0 to 1) |

---

## Basic Syntax Rules

### Rule 1: Functions Must Be EMPTY (No Arguments)

**Correct:**
```sql
ROW_NUMBER()
RANK()
DENSE_RANK()
```

**Wrong:**
```sql
ROW_NUMBER(sales)  ❌
RANK(product)      ❌
```

**Exception:** Only NTILE needs a number!
```sql
NTILE(4)  ✓  -- Divide into 4 groups
```

---

### Rule 2: ORDER BY is REQUIRED

You MUST sort the data to rank it!

**Basic Structure:**
```sql
RANK() OVER (ORDER BY sales DESC)
```

**Parts:**
- `RANK()` = Empty function (no arguments)
- `OVER` = Start window definition
- `ORDER BY sales DESC` = Sort by sales, highest first (REQUIRED!)

---

### Rule 3: PARTITION BY is Optional

Use it if you want to rank **within groups**

**Without PARTITION BY (rank all together):**
```sql
RANK() OVER (ORDER BY sales DESC)
```

**With PARTITION BY (rank within each category):**
```sql
RANK() OVER (PARTITION BY category ORDER BY sales DESC)
```

---

### Rule 4: NO Frame Clause Allowed

You **cannot** use ROWS BETWEEN with ranking functions!

**Wrong:**
```sql
RANK() OVER (
    ORDER BY sales 
    ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING  ❌
)
```

Frame clauses only work with aggregate functions!

---

## Complete Syntax Summary

### All Functions (except NTILE):
```sql
FUNCTION_NAME() OVER (
    [PARTITION BY column]  -- Optional
    ORDER BY column        -- REQUIRED!
)
```

### NTILE (special case):
```sql
NTILE(number) OVER (
    [PARTITION BY column]  -- Optional
    ORDER BY column        -- REQUIRED!
)
```

---

## Quick Reference Table

| Function | Needs Argument? | Needs ORDER BY? | Needs PARTITION BY? | Can Use Frame? |
|----------|----------------|-----------------|-------------------|---------------|
| ROW_NUMBER | No (empty) | Yes (required) | No (optional) | No |
| RANK | No (empty) | Yes (required) | No (optional) | No |
| DENSE_RANK | No (empty) | Yes (required) | No (optional) | No |
| NTILE | Yes (number) | Yes (required) | No (optional) | No |
| PERCENT_RANK | No (empty) | Yes (required) | No (optional) | No |
| CUME_DIST | No (empty) | Yes (required) | No (optional) | No |

---

## Visual Comparison

### Integer-Based vs Percentage-Based

```
INTEGER-BASED (Discrete Values):
1, 2, 3, 4, 5, 6, 7...
↑
Depends on number of rows
Could be 5, 50, 500, 5000...

PERCENTAGE-BASED (Continuous Values):
0.0, 0.15, 0.33, 0.67, 1.0
↑
Always between 0 and 1
Infinite possible values
```

---

## Example Comparison

**Data:**
```
Product | Sales
--------|------
E       | 70
B       | 30
C       | 20
A       | 10
D       | 10
```

**Integer-Based Ranking:**
- Answer specific questions: "Top 3 products"
- Result: E, B, C

**Percentage-Based Ranking:**
- Answer proportional questions: "Top 20% of products"
- Result: E only (20% of 5 products = 1 product)

---

## Key Points to Remember

1. **Sorting happens FIRST** before ranking
2. **Two methods:** Integer (1,2,3...) and Percentage (0 to 1)
3. **Six total functions:** 4 integer-based, 2 percentage-based
4. **Functions are empty** (except NTILE)
5. **ORDER BY is required** - you must sort!
6. **PARTITION BY is optional** - use for groups
7. **NO frame clause** with ranking functions
8. **Integer = Top N**, **Percentage = Top X%**

---

## What's Coming Next?

We'll learn each ranking function in detail:
- **ROW_NUMBER** - Simple numbering (1, 2, 3...)
- **RANK** - Handles ties, skips numbers
- **DENSE_RANK** - Handles ties, no gaps
- **NTILE** - Divides into groups
- **PERCENT_RANK** - Percentage position
- **CUME_DIST** - Cumulative percentage

Each has special uses and rules!

---

## Practice Questions

1. What must SQL do before ranking data?
2. Name the two methods of ranking.
3. Which method uses whole numbers?
4. Which method uses percentages?
5. Is ORDER BY optional or required?
6. Can you use expressions inside RANK()?
7. What's the only function that needs an argument?
8. When would you use "top 5" vs "top 20%"?

---

## Final Takeaway

**Ranking Functions help you:**
- Put data in order
- Find top/bottom performers
- Compare positions
- Understand distributions
- Make better decisions

They're super powerful for competitions, sales analysis, student rankings, and much more!
