# SQL COUNT Function - Study Notes

## What is the COUNT Function?

**COUNT** is a function that tells you **how many rows** you have in your data.

**Think of it like:** Counting how many students are in your class - that's what COUNT does with data!

---

## Basic Example

Let's say you have a table with products and sales:

| Product | Sales |
|---------|-------|
| Cap | 50 |
| Cap | 75 |
| Cap | 50 |
| Glove | 60 |
| Glove | 70 |
| Glove | NULL |

**Question:** How many orders do we have for each product?

**Answer using COUNT:**
```sql
COUNT(*) OVER (PARTITION BY product)
```

**Result:**
- Caps: 3 orders
- Gloves: 3 orders

---

## Two Ways to Use COUNT

### 1. COUNT(*) - Count ALL Rows
```sql
COUNT(*)
```

**What it does:** Counts EVERY row, including NULLs

**Example:** If you have 3 rows with NULL values, it still counts them!

### 2. COUNT(column_name) - Count NON-NULL Values
```sql
COUNT(sales)
```

**What it does:** Counts only rows that have actual values (ignores NULLs)

**Example:** If you have 3 rows but 1 is NULL, it counts only 2!

---

## Understanding COUNT with NULLs

This is **VERY IMPORTANT**! 

### Example with NULL Values:

| Product | Sales |
|---------|-------|
| Glove | 60 |
| Glove | 70 |
| Glove | NULL |

**Using COUNT(*):**
- Result: **3** (counts all rows, including NULL)

**Using COUNT(sales):**
- Result: **2** (ignores NULL, counts only actual sales)

### When to Use Each:

✅ Use **COUNT(*)** when: You want to know "How many rows exist?"

✅ Use **COUNT(column_name)** when: You want to know "How many actual values do we have?"

---

## COUNT(*) vs COUNT(1)

You might see people use:
- `COUNT(*)` 
- `COUNT(1)`

**Good news:** Both do the SAME thing! They both count all rows including NULLs.

**Which should you use?** Either one! They have similar performance. Most people use `COUNT(*)`.

---

## Special Feature of COUNT

**COUNT is special** because it accepts ANY data type:
- ✅ Numbers (sales, prices, ages)
- ✅ Text (names, products, addresses)
- ✅ Dates (birthdays, order dates)

**Example:**
```sql
COUNT(product_name)  -- Works! Counts text
COUNT(sales)         -- Works! Counts numbers
COUNT(order_date)    -- Works! Counts dates
```

**Remember:** COUNT doesn't count UNIQUE values. If "Cap" appears 3 times, it counts all 3!

---

## Four Important Use Cases

### 1. Overall Analysis (Big Picture Numbers)

**Purpose:** Find total counts for your entire business

**Example Task:** Find total number of orders

```sql
SELECT COUNT(*) AS total_orders
FROM orders
```

**Result:** 10 orders

**Why it's useful:** Helps you understand the size of your business

**Real-life example:** "How many students are in the entire school?" - That's overall analysis!

---

### 2. Category Analysis (Compare Groups)

**Purpose:** Compare different groups or categories

**Example Task:** Find number of orders for EACH customer

```sql
SELECT 
    customer_id,
    COUNT(*) OVER (PARTITION BY customer_id) AS orders_by_customer
FROM orders
```

**Result:**
- Customer 1: 3 orders
- Customer 2: 3 orders  
- Customer 3: 3 orders
- Customer 4: 1 order (Different behavior!)

**Why it's useful:** Helps you understand patterns and differences

**Real-life example:** "How many students are in each class?" - That's category analysis!

---

### 3. Check for NULL Values (Data Quality Check)

**Purpose:** Find missing data in your tables

**Example Task:** Find how many customers have scores vs total customers

```sql
SELECT 
    COUNT(*) AS total_customers,      -- All customers
    COUNT(score) AS total_scores      -- Only customers with scores
FROM customers
```

**Result:**
- Total customers: 5
- Total scores: 4
- **Missing scores: 1** (5 - 4 = 1 NULL!)

**Why it's useful:** Quickly spot missing data without checking every row!

**Real-life example:** "How many students submitted homework?" - Quick quality check!

---

### 4. Find Duplicate Data (Data Quality Check)

**Purpose:** Discover if you have duplicate records (very important!)

**What are duplicates?** Same data appearing multiple times when it should be unique

**Example:** Two orders with the same Order ID (should never happen!)

#### Step 1: Understand Primary Keys

**Primary Key:** A special column that should be UNIQUE (like Student ID numbers)

**Example Primary Keys:**
- Order ID (each order should have unique number)
- Product ID (each product should have unique number)
- Student ID (each student should have unique number)

#### Step 2: Check for Duplicates

```sql
SELECT 
    order_id,
    COUNT(*) OVER (PARTITION BY order_id) AS check_primary_key
FROM orders
```

**What you expect:**
- Every Order ID should have count = **1** (meaning unique!)

**What happens if data is bad:**
- Some Order IDs might have count = **2 or 3** (meaning duplicates! 🚨)

#### Step 3: Get List of Duplicates

```sql
SELECT *
FROM (
    SELECT 
        order_id,
        COUNT(*) OVER (PARTITION BY order_id) AS check_primary_key
    FROM orders
) AS subquery
WHERE check_primary_key > 1
```

**Result:** Shows ONLY the Order IDs that have duplicates!

**Why this is important:**
- Bad data = Bad analysis = Bad decisions
- Duplicates mess up your counts and totals
- You must clean data before analyzing it!

**Real-life example:** Finding if two students accidentally got the same ID number!

---

## Important Syntax Reminders

### With Window Functions (OVER clause):

```sql
COUNT(*) OVER (PARTITION BY column_name)
```

**Use when:** You want to keep all rows AND add count information

### Without Window Functions:

```sql
COUNT(*)
```

**Use when:** You just want one total number

---

## Quick Practice Examples

### Example 1: Simple Count
**Question:** How many products do we have?

```sql
SELECT COUNT(*) AS total_products
FROM products
```

### Example 2: Count by Category
**Question:** How many products in each category?

```sql
SELECT 
    category,
    COUNT(*) OVER (PARTITION BY category) AS products_per_category
FROM products
```

### Example 3: Check Data Quality
**Question:** Do all customers have email addresses?

```sql
SELECT 
    COUNT(*) AS total_customers,
    COUNT(email) AS customers_with_email
FROM customers
```

### Example 4: Find Duplicates
**Question:** Are there duplicate customer IDs?

```sql
SELECT 
    customer_id,
    COUNT(*) OVER (PARTITION BY customer_id) AS check_duplicates
FROM customers
```

Look for any result > 1!

---

## Key Takeaways

✅ **COUNT(*)** counts everything including NULLs

✅ **COUNT(column)** counts only non-NULL values

✅ COUNT works with ANY data type (numbers, text, dates)

✅ Use COUNT for **overall analysis** (total numbers)

✅ Use COUNT with **PARTITION BY** for category analysis

✅ Use COUNT to **check for NULLs** (data quality)

✅ Use COUNT to **find duplicates** (very important for clean data!)

✅ Always check for duplicates BEFORE analyzing data

---

## Common Mistakes to Avoid

❌ **Mistake:** Using COUNT(*) when you should ignore NULLs
✅ **Fix:** Use COUNT(column_name) instead

❌ **Mistake:** Thinking COUNT removes duplicates
✅ **Fix:** COUNT counts all occurrences, use DISTINCT for unique counts

❌ **Mistake:** Not checking for duplicates in your data
✅ **Fix:** Always check primary keys for duplicates first!

---

## What's Next?

Now that you understand COUNT, we'll learn about the **SUM** function to add up numbers! 🎯
