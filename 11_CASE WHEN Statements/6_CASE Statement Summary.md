# SQL CASE Statements: Complete Summary
## A Simple Guide for 7th Grade Students

## What is a CASE Statement? 🤔

A **CASE statement** is like a decision-maker in SQL. It checks conditions one by one and returns a result when it finds the first TRUE condition.

**Think of it like this:**
```
IF condition 1 is true → Do this
ELSE IF condition 2 is true → Do that
ELSE → Do something else
```

---

## The One Important Rule ⚠️

**All results must have the SAME data type!**

✅ **Correct:** All text OR all numbers
```sql
CASE
    WHEN score > 90 THEN 'A'
    WHEN score > 80 THEN 'B'
    ELSE 'C'
END
```

❌ **Wrong:** Mixing text and numbers
```sql
CASE
    WHEN score > 90 THEN 'A'
    WHEN score > 80 THEN 2    -- ERROR!
    ELSE 'C'
END
```

---

## The Four Main Uses of CASE Statements

### 1️⃣ Categorizing Data
### 2️⃣ Mapping Values
### 3️⃣ Handling NULLs
### 4️⃣ Conditional Aggregations

Let's look at each one with examples!

---

## Use Case 1: Categorizing Data 📊

**Purpose:** Group data into categories based on rules

### Example: Categorize Sales

**Sample Data:**
```
order_id | sales
---------|-------
1        | 15
2        | 60
3        | 35
4        | 50
5        | 75
```

**Query:**
```sql
SELECT 
    order_id,
    sales,
    CASE
        WHEN sales > 50 THEN 'High'
        WHEN sales > 20 THEN 'Medium'
        ELSE 'Low'
    END AS category
FROM sales_orders;
```

**Output:**
```
order_id | sales | category
---------|-------|----------
1        | 15    | Low
2        | 60    | High
3        | 35    | Medium
4        | 50    | Medium
5        | 75    | High
```

**Then aggregate by category:**
```sql
SELECT 
    category,
    SUM(sales) AS total_sales
FROM (
    SELECT 
        sales,
        CASE
            WHEN sales > 50 THEN 'High'
            WHEN sales > 20 THEN 'Medium'
            ELSE 'Low'
        END AS category
    FROM sales_orders
) AS categorized
GROUP BY category
ORDER BY total_sales DESC;
```

**Output:**
```
category | total_sales
---------|-------------
High     | 135
Medium   | 85
Low      | 15
```

---

## Use Case 2: Mapping Values 🗺️

**Purpose:** Convert codes to readable text (or vice versa)

### Example A: Gender Codes to Full Text

**Sample Data:**
```
employee_id | name  | gender
------------|-------|--------
1           | Sarah | F
2           | Mike  | M
3           | Emma  | F
```

**Query:**
```sql
SELECT 
    employee_id,
    name,
    gender,
    CASE
        WHEN gender = 'F' THEN 'Female'
        WHEN gender = 'M' THEN 'Male'
        ELSE 'Not Available'
    END AS gender_full
FROM employees;
```

**Output:**
```
employee_id | name  | gender | gender_full
------------|-------|--------|-------------
1           | Sarah | F      | Female
2           | Mike  | M      | Male
3           | Emma  | F      | Female
```

---

### Example B: Country Names to Abbreviations

**Sample Data:**
```
customer_id | country
------------|----------
1           | Germany
2           | USA
3           | Germany
```

**Query:**
```sql
SELECT 
    customer_id,
    country,
    CASE country
        WHEN 'Germany' THEN 'DE'
        WHEN 'USA' THEN 'US'
        ELSE 'NA'
    END AS country_code
FROM customers;
```

**Output:**
```
customer_id | country | country_code
------------|---------|-------------
1           | Germany | DE
2           | USA     | US
3           | Germany | DE
```

---

## Use Case 3: Handling NULLs 🔧

**Purpose:** Replace missing values (NULLs) with something meaningful

### Example: Replace NULL Scores with Zero

**Sample Data:**
```
customer_id | name     | score
------------|----------|-------
1           | Smith    | 500
2           | Johnson  | 750
3           | Williams | NULL
4           | Brown    | 600
```

**Query:**
```sql
SELECT 
    customer_id,
    name,
    score,
    CASE
        WHEN score IS NULL THEN 0
        ELSE score
    END AS score_clean
FROM customers;
```

**Output:**
```
customer_id | name     | score | score_clean
------------|----------|-------|-------------
1           | Smith    | 500   | 500
2           | Johnson  | 750   | 750
3           | Williams | NULL  | 0
4           | Brown    | 600   | 600
```

**Calculate Average with Clean Data:**
```sql
SELECT 
    AVG(
        CASE
            WHEN score IS NULL THEN 0
            ELSE score
        END
    ) AS average_score
FROM customers;
```

**Output:**
```
average_score
--------------
462.5
```
(Calculation: (500 + 750 + 0 + 600) ÷ 4 = 462.5)

---

## Use Case 4: Conditional Aggregations 🎯

**Purpose:** Count or sum only data that meets specific conditions

### Example: Count High-Value Orders

**Sample Data:**
```
order_id | customer_id | sales
---------|-------------|-------
1        | 1           | 15
2        | 1           | 60
3        | 1           | 25
4        | 2           | 20
5        | 2           | 45
6        | 3           | 50
```

**Query:**
```sql
SELECT 
    customer_id,
    COUNT(*) AS total_orders,
    SUM(
        CASE
            WHEN sales > 30 THEN 1
            ELSE 0
        END
    ) AS high_value_orders
FROM orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | total_orders | high_value_orders
------------|--------------|-------------------
1           | 3            | 1
2           | 2            | 1
3           | 1            | 1
```

**Explanation:**
- Customer 1: 3 total orders, only 1 over $30 (the $60 order)
- Customer 2: 2 total orders, only 1 over $30 (the $45 order)
- Customer 3: 1 total order, and it's over $30 (the $50 order)

---

## Complete Example: All Uses Combined! 🌟

Let's create a comprehensive report using all CASE techniques:

**Sample Data:**
```
order_id | customer | country | sales | status
---------|----------|---------|-------|--------
1        | John     | USA     | 60    | 1
2        | Anna     | Germany | NULL  | 1
3        | Mike     | USA     | 25    | 0
4        | Lisa     | Germany | 45    | 1
```

**Query:**
```sql
SELECT 
    order_id,
    customer,
    -- Use Case 2: Mapping
    CASE country
        WHEN 'USA' THEN 'US'
        WHEN 'Germany' THEN 'DE'
        ELSE 'Other'
    END AS country_code,
    -- Use Case 3: Handling NULLs
    CASE
        WHEN sales IS NULL THEN 0
        ELSE sales
    END AS sales_clean,
    -- Use Case 1: Categorizing
    CASE
        WHEN sales > 50 THEN 'High'
        WHEN sales > 20 THEN 'Medium'
        ELSE 'Low'
    END AS sales_category,
    -- Use Case 2: Mapping status
    CASE status
        WHEN 1 THEN 'Active'
        WHEN 0 THEN 'Inactive'
        ELSE 'Unknown'
    END AS status_text
FROM orders;
```

**Output:**
```
order_id | customer | country_code | sales_clean | sales_category | status_text
---------|----------|--------------|-------------|----------------|-------------
1        | John     | US           | 60          | High           | Active
2        | Anna     | DE           | 0           | Low            | Active
3        | Mike     | US           | 25          | Medium         | Inactive
4        | Lisa     | DE           | 45          | Medium         | Active
```

---

## Quick Reference Card 📝

| Use Case | What It Does | Key Pattern |
|----------|--------------|-------------|
| **Categorizing** | Groups data into categories | `WHEN value > X THEN 'Category'` |
| **Mapping** | Converts codes to text | `WHEN code = 'A' THEN 'Description'` |
| **Handling NULLs** | Replaces missing values | `WHEN column IS NULL THEN default` |
| **Conditional Aggregations** | Counts/sums with conditions | `SUM(CASE WHEN condition THEN 1 ELSE 0 END)` |

---

## Summary 🎓

CASE statements are **data transformation tools** that help you:
- ✅ Create new columns with useful information
- ✅ Make data easier to read and understand
- ✅ Perform smart calculations with conditions
- ✅ Build better reports for analysis

**Remember:** CASE statements evaluate conditions from top to bottom and stop at the first TRUE condition!

**The Golden Rule:** Keep all your results the same data type! 🌟
