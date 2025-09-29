# SQL AVG (Average) Function - Study Notes

## What is the AVG Function?

**AVG** is a function that finds the **average** (middle value) of numbers in a group.

**How to calculate average:** Add all numbers together, then divide by how many numbers you have.

**Example:** Your test scores are 80, 90, 100
- Add them: 80 + 90 + 100 = 270
- Divide by 3: 270 ÷ 3 = **90** (your average!)

---

## Basic Example

Let's say you have a table with products and sales:

| Product | Sales |
|---------|-------|
| Cap | 20 |
| Cap | 10 |
| Cap | 5 |
| Glove | 30 |
| Glove | 70 |
| Glove | NULL |

**Question:** What's the average sales for each product?

**Answer using AVG:**
```sql
AVG(sales) OVER (PARTITION BY product)
```

**How SQL calculates it:**

**For Caps:**
- Add: 20 + 10 + 5 = 35
- Divide by 3 rows: 35 ÷ 3 = **11.67**

**For Gloves:**
- Add: 30 + 70 = 100 (NULL is ignored!)
- Divide by 2 rows: 100 ÷ 2 = **50**

---

## CRITICAL: How AVG Handles NULLs

This is **VERY TRICKY** and causes mistakes! Pay close attention!

### Default Behavior: NULLs Are Ignored

By default, AVG **skips NULL values** in both:
1. The sum (adding numbers)
2. The count (counting how many)

### Example That Shows the Problem:

**Scenario:** You have 3 sales: 30, 70, NULL

**What SQL does by default:**
- Adds: 30 + 70 = 100 (ignores NULL)
- Divides by: 2 (only counts non-NULL rows)
- Average: 100 ÷ 2 = **50**

**But what if NULL means ZERO sales?**

Then the CORRECT calculation should be:
- Adds: 30 + 70 + 0 = 100
- Divides by: 3 (all rows including the zero)
- Average: 100 ÷ 3 = **33.33**

**See the difference?** 50 vs 33.33 - That's a BIG mistake!

---

## The NULL Problem Explained Simply

Think about it this way:

**Situation 1:** Your friend didn't take a test (NULL = missing data)
- You wouldn't count their score as zero
- You'd calculate average without them
- **NULL should be ignored**

**Situation 2:** Your friend took the test but scored zero (NULL = actually zero)
- You WOULD count their zero
- They're part of the class average
- **NULL should be replaced with 0**

**The key question:** What does NULL mean in YOUR data?
- Missing data? Leave it alone (SQL handles it)
- Actually zero? Replace it before calculating!

---

## How to Handle NULLs Correctly

### Step 1: Understand What NULL Means

**Ask yourself or your business team:**
- Does NULL mean "no data" or "zero"?
- Should this be counted in the average?

### Step 2: Use COALESCE to Replace NULLs

**COALESCE** replaces NULL with a value you choose

**Syntax:**
```sql
COALESCE(column_name, replacement_value)
```

**Example:**
```sql
COALESCE(sales, 0)  -- Replaces NULL with 0
```

### Step 3: Calculate Average with Handled NULLs

**Wrong way (ignores NULL):**
```sql
AVG(sales)  -- Divides by only non-NULL rows
```

**Right way (treats NULL as zero):**
```sql
AVG(COALESCE(sales, 0))  -- Replaces NULL with 0 BEFORE averaging
```

---

## Complete Example with NULL Handling

Let's work through a real problem!

**Table: Customers**

| Customer ID | Last Name | Score |
|-------------|-----------|-------|
| 1 | Smith | 500 |
| 2 | Jones | 750 |
| 3 | Brown | 600 |
| 4 | Davis | 650 |
| 5 | Wilson | NULL |

### Task: Find average customer score

#### Attempt 1: Without Handling NULLs (WRONG if NULL = zero)

```sql
SELECT 
    customer_id,
    last_name,
    score,
    AVG(score) OVER () AS average_score
FROM customers
```

**Result:** Average = 625
- Calculation: (500 + 750 + 600 + 650) ÷ 4 = 625
- Problem: NULL was ignored! If it should be zero, this is wrong!

#### Attempt 2: With NULL Handling (CORRECT if NULL = zero)

**Step 1: Replace NULL with 0**
```sql
SELECT 
    customer_id,
    last_name,
    score,
    COALESCE(score, 0) AS customer_score
FROM customers
```

**Result:** Wilson now shows 0 instead of NULL

**Step 2: Calculate average correctly**
```sql
SELECT 
    customer_id,
    last_name,
    score,
    COALESCE(score, 0) AS customer_score,
    AVG(COALESCE(score, 0)) OVER () AS average_score_without_nulls
FROM customers
```

**Result:** Average = 500
- Calculation: (500 + 750 + 600 + 650 + 0) ÷ 5 = 500
- This is correct if NULL means zero!

---

## Two Main Use Cases

### Use Case 1: Overall Analysis

**Purpose:** Find average for entire dataset

**Example Task:** Find average sales across ALL orders

```sql
SELECT 
    order_id,
    order_date,
    sales,
    AVG(sales) OVER () AS average_sales
FROM orders
```

**Note:** Empty `OVER ()` means calculate average of everything

**Result:** Every row shows the same overall average (like 38)

### Use Case 2: Category Analysis

**Purpose:** Find average for each group

**Example Task:** Find average sales for EACH product

```sql
SELECT 
    order_id,
    product_id,
    sales,
    AVG(sales) OVER (PARTITION BY product_id) AS average_sales_by_product
FROM orders
```

**Result Example:**
- Product 101: Average = $35 per order
- Product 102: Average = $45 per order
- Product 103: Average = $60 per order

**Why useful:** Compare typical performance between products!

---

## Use Case 3: Comparison Analysis - Above or Below Average?

This is a powerful way to understand performance!

### The Big Question: "Is this better or worse than typical?"

**Example:** Is a student's test score above or below class average?

### Real SQL Example

**Task:** Find all orders with sales HIGHER than average sales

#### Step 1: Get sales and average

```sql
SELECT 
    order_id,
    product_id,
    sales,
    AVG(sales) OVER () AS average_sales
FROM orders
```

**Result:**
- Order 1: Sales = 20, Average = 38 (below average)
- Order 4: Sales = 60, Average = 38 (above average!)
- Order 7: Sales = 45, Average = 38 (above average!)

#### Step 2: Filter to show only above-average orders

**Problem:** Can't use window functions in WHERE clause directly!

**Solution:** Use a subquery (query inside a query)

```sql
SELECT *
FROM (
    SELECT 
        order_id,
        product_id,
        sales,
        AVG(sales) OVER () AS average_sales
    FROM orders
) AS subquery
WHERE sales > average_sales
```

**Result:** Shows only orders 4, 7, and others above 38

**Why this matters:** 
- Identifies top performers
- Shows which orders are exceptional
- Helps understand what "good" looks like

---

## Why Comparison with Average is Important

**In Business:**
- Which products sell better than typical?
- Which customers spend more than average?
- Which months perform above expectations?

**In School:**
- Is your score above class average?
- Are you improving faster than typical?
- Which subjects are you strongest in?

**Key insight:** Average shows you what's "normal" - then you can see what's special!

---

## Important Rules for AVG

### Rule 1: Numbers Only

AVG only works with numbers (just like SUM)

✅ Works: `AVG(sales)`, `AVG(score)`, `AVG(price)`

❌ Doesn't work: `AVG(name)`, `AVG(product)`

### Rule 2: Be Careful with NULLs!

**Always ask:** What does NULL mean in this data?
- Missing data → Use regular AVG
- Actually zero → Use AVG(COALESCE(column, 0))

### Rule 3: Can't Use Window Functions in WHERE

**Wrong:**
```sql
WHERE AVG(sales) OVER () > 100  -- ERROR!
```

**Right:**
```sql
SELECT * FROM (
    SELECT AVG(sales) OVER () AS avg_sales
    FROM orders
) WHERE avg_sales > 100
```

---

## Step-by-Step Practice Example

Let's calculate class test averages!

**Your data:**

| Student | Test Score |
|---------|------------|
| Alice | 85 |
| Bob | 90 |
| Carol | 75 |
| David | NULL |

**Question 1:** What's the average if NULL means "didn't take test"?

```sql
AVG(test_score)
```
**Answer:** (85 + 90 + 75) ÷ 3 = **83.33**

**Question 2:** What's the average if NULL means "scored zero"?

```sql
AVG(COALESCE(test_score, 0))
```
**Answer:** (85 + 90 + 75 + 0) ÷ 4 = **62.5**

**See the huge difference?** 83.33 vs 62.5!

---

## Key Takeaways

✅ AVG adds numbers and divides by count

✅ By default, AVG **ignores NULLs** (doesn't count them)

✅ **Always understand what NULL means** before calculating average

✅ Use **COALESCE** to replace NULL with 0 if needed

✅ Use empty `OVER ()` for overall average

✅ Use `PARTITION BY` for group averages

✅ **Comparison analysis** helps identify above/below average performance

✅ Use **subqueries** to filter by window function results

---

## Common Mistakes to Avoid

❌ **Mistake:** Ignoring NULLs when they should be zeros
✅ **Fix:** Use AVG(COALESCE(column, 0))

❌ **Mistake:** Not understanding what NULL means in your data
✅ **Fix:** Always ask business users what NULL represents

❌ **Mistake:** Getting different averages than expected
✅ **Fix:** Check if NULLs are being handled correctly

❌ **Mistake:** Trying to use window functions in WHERE clause
✅ **Fix:** Use a subquery instead

---

## Quick Reference

**Find overall average:**
```sql
AVG(column) OVER ()
```

**Find average by group:**
```sql
AVG(column) OVER (PARTITION BY group_column)
```

**Handle NULLs as zero:**
```sql
AVG(COALESCE(column, 0)) OVER ()
```

**Find above-average values:**
```sql
SELECT * FROM (
    SELECT column, AVG(column) OVER () AS avg_value
    FROM table
) WHERE column > avg_value
```

---

## What's Next?

Now that you understand AVG, we'll learn about **MIN and MAX** functions to find smallest and largest values!
