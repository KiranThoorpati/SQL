# SQL CASE Statements: Handling Nulls & Conditional Aggregations
## A Simple Guide for 7th Grade Students

## Part 1: What are NULL Values?

**NULL** means "no value" or "missing information" in a database.

**Real-Life Example:**
- You have a list of test scores: 85, 90, NULL, 75
- The NULL means someone didn't take the test yet
- It's NOT zero - it's just missing!

---

## Why are NULLs a Problem?

NULLs can cause **wrong calculations**!

**Example:**
- Scores: 80, 90, 70, NULL
- If we calculate average: (80 + 90 + 70) ÷ 3 = 80
- But if we treat NULL as 0: (80 + 90 + 70 + 0) ÷ 4 = 60

**Big difference!** We need to handle NULLs correctly based on what our business needs.

---

## Use Case 1: Replacing NULLs with Zero

### The Problem

Let's say we have customer scores, and some are missing:

```sql
SELECT 
    customer_id,
    last_name,
    score
FROM sales_customers;
```

**Output:**
```
customer_id | last_name | score
------------|-----------|-------
1           | Smith     | 500
2           | Johnson   | 750
3           | Williams  | NULL
4           | Brown     | 600
5           | Davis     | 650
```

---

### Calculating Average WITHOUT Handling NULL

```sql
SELECT 
    customer_id,
    last_name,
    score,
    AVG(score) OVER() AS average_score
FROM sales_customers;
```

**Output:**
```
customer_id | last_name | score | average_score
------------|-----------|-------|---------------
1           | Smith     | 500   | 625
2           | Johnson   | 750   | 625
3           | Williams  | NULL  | 625
4           | Brown     | 600   | 625
5           | Davis     | 650   | 625
```

**Calculation:** (500 + 750 + 600 + 650) ÷ 4 = 625

❌ **Problem:** SQL ignored the NULL and only counted 4 customers!

---

### Solution: Replace NULL with Zero Using CASE

```sql
SELECT 
    customer_id,
    last_name,
    score,
    CASE
        WHEN score IS NULL THEN 0
        ELSE score
    END AS score_clean
FROM sales_customers;
```

**Output:**
```
customer_id | last_name | score | score_clean
------------|-----------|-------|-------------
1           | Smith     | 500   | 500
2           | Johnson   | 750   | 750
3           | Williams  | NULL  | 0
4           | Brown     | 600   | 600
5           | Davis     | 650   | 650
```

**How it works:**
- If score IS NULL → Replace with 0
- If score is NOT NULL → Keep the original score

---

### Calculating Correct Average with Clean Data

```sql
SELECT 
    customer_id,
    last_name,
    score,
    AVG(
        CASE
            WHEN score IS NULL THEN 0
            ELSE score
        END
    ) OVER() AS average_customer_clean
FROM sales_customers;
```

**Output:**
```
customer_id | last_name | score | average_customer_clean
------------|-----------|-------|------------------------
1           | Smith     | 500   | 500
2           | Johnson   | 750   | 500
3           | Williams  | NULL  | 500
4           | Brown     | 600   | 500
5           | Davis     | 650   | 500
```

**Calculation:** (500 + 750 + 0 + 600 + 650) ÷ 5 = 500

✅ **Correct!** Now we're counting all 5 customers!

---

## Important: Checking for NULL

**Wrong way:** ❌ `score = NULL`

**Correct way:** ✅ `score IS NULL`

---

## Part 2: Conditional Aggregations

**Conditional Aggregation** means counting or summing data that meets specific conditions.

**Real-Life Example:**
- Count how many times you scored above 90
- NOT all your test scores, just the ones above 90

---

## Use Case 2: Counting Orders Based on a Condition

### The Task
Count how many times each customer made an order with sales greater than $30.

### Step 1: Look at the Original Data

```sql
SELECT 
    order_id,
    customer_id,
    sales
FROM sales_orders
ORDER BY customer_id;
```

**Output:**
```
order_id | customer_id | sales
---------|-------------|-------
1        | 1           | 15
4        | 1           | 60
7        | 1           | 25
2        | 2           | 20
5        | 2           | 10
8        | 2           | 30
3        | 3           | 45
6        | 3           | 50
9        | 4           | 35
10       | 4           | 20
```

**Analysis:**
- Customer 1: Has 3 orders, but only 1 is over $30 (order 4 with $60)
- Customer 2: Has 3 orders, but 0 are over $30
- Customer 3: Has 2 orders, and both are over $30
- Customer 4: Has 2 orders, but only 1 is over $30

---

### Step 2: Create a Flag Using CASE

We'll flag each order: **1** if sales > 30, **0** otherwise

```sql
SELECT 
    order_id,
    customer_id,
    sales,
    CASE
        WHEN sales > 30 THEN 1
        ELSE 0
    END AS sales_flag
FROM sales_orders
ORDER BY customer_id;
```

**Output:**
```
order_id | customer_id | sales | sales_flag
---------|-------------|-------|------------
1        | 1           | 15    | 0
4        | 1           | 60    | 1
7        | 1           | 25    | 0
2        | 2           | 20    | 0
5        | 2           | 10    | 0
8        | 2           | 30    | 0
3        | 3           | 45    | 1
6        | 3           | 50    | 1
9        | 4           | 35    | 1
10       | 4           | 20    | 0
```

**Now we can see clearly:**
- Customer 1: Flags are 0, 1, 0 → Sum = 1
- Customer 2: Flags are 0, 0, 0 → Sum = 0
- Customer 3: Flags are 1, 1 → Sum = 2
- Customer 4: Flags are 1, 0 → Sum = 1

---

### Step 3: Sum the Flags (Conditional Count)

```sql
SELECT 
    customer_id,
    SUM(
        CASE
            WHEN sales > 30 THEN 1
            ELSE 0
        END
    ) AS high_sales_orders
FROM sales_orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | high_sales_orders
------------|-------------------
1           | 1
2           | 0
3           | 2
4           | 1
```

✅ **Perfect!** We counted only orders with sales > $30!

---

### Step 4: Compare with Total Orders

Let's see both regular count AND conditional count:

```sql
SELECT 
    customer_id,
    COUNT(*) AS total_orders,
    SUM(
        CASE
            WHEN sales > 30 THEN 1
            ELSE 0
        END
    ) AS high_sales_orders
FROM sales_orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | total_orders | high_sales_orders
------------|--------------|-------------------
1           | 3            | 1
2           | 3            | 0
3           | 2            | 2
4           | 2            | 1
```

**This shows:**
- Customer 1: Made 3 orders total, only 1 over $30
- Customer 2: Made 3 orders total, none over $30
- Customer 3: Made 2 orders total, both over $30
- Customer 4: Made 2 orders total, only 1 over $30

---

## The Trick: Using 1s and 0s

When you use **1** for TRUE and **0** for FALSE, you can sum them to count!

**Example:**
- Orders: 1, 0, 1, 0, 1
- Sum = 1 + 0 + 1 + 0 + 1 = **3** orders met the condition!

---

## More Practice Examples

### Example 1: Count Failed Tests

```sql
SELECT 
    student_id,
    SUM(
        CASE
            WHEN score < 60 THEN 1
            ELSE 0
        END
    ) AS failed_tests
FROM test_results
GROUP BY student_id;
```

---

### Example 2: Count Weekend Sales

```sql
SELECT 
    store_id,
    SUM(
        CASE
            WHEN day_of_week IN ('Saturday', 'Sunday') THEN sales_amount
            ELSE 0
        END
    ) AS weekend_sales
FROM daily_sales
GROUP BY store_id;
```

---

## Summary

### Handling NULLs
- **NULL** means missing data
- Use `score IS NULL` (not `score = NULL`)
- Replace NULLs to avoid wrong calculations
- Use CASE to replace: `WHEN score IS NULL THEN 0`

### Conditional Aggregations
- Count or sum only data that meets conditions
- Use flags (1 = yes, 0 = no)
- Sum the flags to get your count!
- Helps you analyze specific subsets of data

**Remember:** CASE statements give you powerful control over how you calculate things! 🎯
