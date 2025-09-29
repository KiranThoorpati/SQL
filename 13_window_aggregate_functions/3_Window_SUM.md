# SQL SUM Function - Study Notes

## What is the SUM Function?

**SUM** is a function that **adds up all the numbers** in a group.

**Think of it like:** Adding up all your test scores to see your total points - that's what SUM does with data!

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

**Question:** What are the total sales for each product?

**Answer using SUM:**
```sql
SUM(sales) OVER (PARTITION BY product)
```

**How SQL does it:**
1. Divides data into groups (Caps and Gloves)
2. Adds up sales in each group separately

**Result:**
- **Caps:** 20 + 10 + 5 = **35**
- **Gloves:** 30 + 70 = **100** (NULL is ignored!)

---

## How SUM Handles NULLs

**Important:** SUM automatically **ignores NULL values**!

### Example:
If you have sales: 30, 70, NULL

**SUM result:** 30 + 70 = **100** (NULL is skipped)

**Think of it like:** If you're adding test scores and someone didn't take a test, you don't count their missing score as zero - you just skip it!

---

## Important Rules for SUM

### Rule 1: Numbers Only! 📊

SUM only works with **numbers** (integers, decimals)

✅ **Works:**
```sql
SUM(sales)        -- Numbers like 20, 30, 50
SUM(price)        -- Numbers like 10.50, 25.99
SUM(quantity)     -- Numbers like 5, 10, 15
```

❌ **Doesn't Work:**
```sql
SUM(product_name)  -- Text like "Cap", "Glove"
SUM(customer)      -- Text like "John", "Sarah"
```

### Rule 2: NULLs are Ignored

Unlike COUNT, SUM doesn't need special handling for NULLs - they're automatically skipped!

---

## Two Main Use Cases

### Use Case 1: Overall Analysis (Total for Everything)

**Purpose:** Find one big total for your entire business

**Example Task:** Find total sales across ALL orders

```sql
SELECT 
    order_id,
    order_date,
    sales,
    SUM(sales) OVER () AS total_sales
FROM orders
```

**Note:** Empty `OVER ()` means "add up everything, don't divide into groups"

**Result:** Every row shows the same grand total

**Why it's useful:** You can see individual sales AND the big picture at the same time!

**Real-life example:** "What's my total points from ALL tests this year?" - That's overall analysis!

---

### Use Case 2: Category Analysis (Total for Each Group)

**Purpose:** Find totals for different groups and compare them

**Example Task:** Find total sales for EACH product

```sql
SELECT 
    order_id,
    product_id,
    sales,
    SUM(sales) OVER (PARTITION BY product_id) AS sales_by_product
FROM orders
```

**Note:** `PARTITION BY product_id` means "divide into product groups and add up each group"

**Result Example:**
- Product 101: $500 (Highest sales! 🏆)
- Product 102: $300
- Product 105: $150 (Lowest sales)

**Why it's useful:** Compare performance between different products, customers, or categories!

**Real-life example:** "What's my total points in Math vs Science?" - That's category analysis!

---

## Special Use Case: Comparison Analysis 🔍

This is a **very powerful** way to use SUM!

### What is Comparison Analysis?

**Comparing** a current value with a total or average to understand its importance

### Types of Comparisons:

#### 1. **Part-to-Whole Analysis** (Most Common!)

Compare one piece to the total

**Example:** How much did March contribute to the entire year's sales?

### Let's Do a Real Example!

**Task:** Find what percentage each product contributes to total sales

#### Step 1: Get Basic Data
```sql
SELECT 
    order_id,
    product_id,
    sales
FROM orders
```

**Result:** Individual sales numbers

#### Step 2: Add Total Sales
```sql
SELECT 
    order_id,
    product_id,
    sales,
    SUM(sales) OVER () AS total_sales
FROM orders
```

**Result:** Now you have both individual sales AND grand total!

#### Step 3: Calculate Percentage

**Formula:** (Individual Sale ÷ Total Sales) × 100

```sql
SELECT 
    order_id,
    product_id,
    sales,
    SUM(sales) OVER () AS total_sales,
    (sales / SUM(sales) OVER ()) * 100 AS percentage_of_total
FROM orders
```

**Problem:** You get zeros! Why? 🤔

**Reason:** Both numbers are integers, and integer ÷ integer = integer (no decimals!)

#### Step 4: Fix Data Type Issue

**Solution:** Convert one number to FLOAT (decimal number)

```sql
SELECT 
    order_id,
    product_id,
    sales,
    SUM(sales) OVER () AS total_sales,
    (CAST(sales AS FLOAT) / SUM(sales) OVER ()) * 100 AS percentage_of_total
FROM orders
```

**What CAST does:** Changes integer to float so you get decimal answers

**Result:** Now you get percentages like 5.5%, 12.3%, etc.

#### Step 5: Make it Pretty with ROUND

**Problem:** Too many decimals (like 5.555555555%)

**Solution:** Use ROUND to keep only 2 decimal places

```sql
SELECT 
    order_id,
    product_id,
    sales,
    SUM(sales) OVER () AS total_sales,
    ROUND((CAST(sales AS FLOAT) / SUM(sales) OVER ()) * 100, 2) AS percentage_of_total
FROM orders
```

**Final Result:**
- Order 1: 8.50% of total
- Order 2: 12.75% of total
- Order 8: 15.25% of total ← Highest contributor! 🏆

---

## Understanding Percentage Contribution

**What does it tell you?**

If Order 8 is **15.25%** of total sales, that means:
- This one order is responsible for more than 15% of ALL sales!
- It's a very important order
- If you lost this order, you'd lose a big chunk of your business

**Real-life example:** 
If you scored 95 points on one test and your total for all tests is 400 points:
- Percentage = (95 ÷ 400) × 100 = **23.75%**
- This means one test is almost 1/4 of your entire grade!
- You know this test was really important! 📝

---

## Why Comparison Analysis is Important

✅ **Understand performance:** See which products/orders are most important

✅ **Identify winners and losers:** Find your best and worst performers

✅ **Make better decisions:** Know where to focus your efforts

✅ **Spot trends:** See if something is above or below average

---

## Complete Example Walkthrough

Let's pretend you're running a lemonade stand! 🥤

### Your Sales Data:

| Day | Cups Sold |
|-----|-----------|
| Monday | 20 |
| Tuesday | 30 |
| Wednesday | 50 |

### Step 1: Find Total
```sql
SUM(cups_sold) = 20 + 30 + 50 = 100 cups
```

### Step 2: Calculate Each Day's Percentage
- Monday: (20 ÷ 100) × 100 = **20%**
- Tuesday: (30 ÷ 100) × 100 = **30%**
- Wednesday: (50 ÷ 100) × 100 = **50%**

### What This Tells You:
- Wednesday was your **BEST day** - half your sales!
- Monday was your **slowest day** - only 1/5 of sales
- You might want to do whatever you did on Wednesday every day!

---

## Quick Practice Problems

### Problem 1: Simple Sum
**Question:** What are total sales for all orders?

```sql
SELECT SUM(sales) AS total_sales
FROM orders
```

### Problem 2: Sum by Category
**Question:** What are total sales for each customer?

```sql
SELECT 
    customer_id,
    SUM(sales) OVER (PARTITION BY customer_id) AS customer_total
FROM orders
```

### Problem 3: Percentage
**Question:** What percent is each sale of the total?

```sql
SELECT 
    sales,
    ROUND((CAST(sales AS FLOAT) / SUM(sales) OVER ()) * 100, 2) AS percent_of_total
FROM orders
```

---

## Key Takeaways

✅ **SUM** adds up all numbers in a group

✅ **NULLs are automatically ignored** - no special handling needed!

✅ **Only works with numbers** - can't sum text or dates

✅ Use **empty OVER()** for grand total

✅ Use **PARTITION BY** to sum different groups separately

✅ **Comparison analysis** helps understand importance and performance

✅ Use **CAST to FLOAT** when dividing to get decimals

✅ Use **ROUND** to clean up decimal places

---

## Common Mistakes to Avoid

❌ **Mistake:** Getting zeros when calculating percentages
✅ **Fix:** Use CAST(column AS FLOAT) before dividing

❌ **Mistake:** Too many decimal places (5.555555%)
✅ **Fix:** Use ROUND(value, 2) for 2 decimal places

❌ **Mistake:** Trying to SUM text columns
✅ **Fix:** Only use SUM with number columns

❌ **Mistake:** Forgetting that NULLs are ignored
✅ **Fix:** Remember SUM skips NULLs automatically (this is usually good!)

---

## What's Next?

Now that you understand SUM, we'll learn about the **AVG (Average)** function to find typical values! 📊
