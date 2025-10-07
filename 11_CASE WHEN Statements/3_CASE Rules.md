# SQL CASE Statement: Important Rules
## A Simple Guide for 7th Grade Students

## The Golden Rule of CASE Statements

There is **ONE very important rule** you must follow when using CASE statements:

> **All results in a CASE statement must have the SAME data type!**

Think of it like sorting toys into boxes:
- If you have a box labeled "toys," you can't suddenly put a sandwich in it!
- Everything must be the same type of thing.

---

## Understanding Data Types

In SQL, there are different types of data:

| Data Type | Examples | What it is |
|-----------|----------|------------|
| **String (Text)** | 'High', 'Low', 'Apple' | Letters and words (use quotes) |
| **Integer (Number)** | 1, 2, 100, 500 | Whole numbers (no quotes) |
| **Decimal** | 10.5, 99.99, 3.14 | Numbers with decimal points |

---

## ✅ CORRECT Example (All Same Data Type)

This works because all results are **strings** (text):

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

✅ **Why it works:** 'High', 'Medium', and 'Low' are all **strings** (text in quotes).

---

## ❌ WRONG Example 1 (Mixed Data Types)

This will give an **ERROR**:

```sql
SELECT 
    order_id,
    sales,
    CASE
        WHEN sales > 50 THEN 'High'
        WHEN sales > 20 THEN 2          -- This is a NUMBER!
        ELSE 'Low'
    END AS category
FROM sales_orders;
```

**Error Message:**
```
ERROR: invalid input syntax for type integer: "Low"
SQL is trying to convert "Low" to a number, which doesn't work!
```

❌ **Why it fails:** We're mixing **strings** ('High', 'Low') with a **number** (2).

---

## ❌ WRONG Example 2 (ELSE with Different Type)

This will also give an **ERROR**:

```sql
SELECT 
    order_id,
    sales,
    CASE
        WHEN sales > 50 THEN 'High'
        WHEN sales > 20 THEN 'Medium'
        ELSE 1                           -- This is a NUMBER!
    END AS category
FROM sales_orders;
```

**Error Message:**
```
ERROR: CASE types text and integer cannot be matched
```

❌ **Why it fails:** The ELSE part returns a **number** (1), but the WHEN parts return **strings** ('High', 'Medium').

---

## ✅ CORRECT Example (All Numbers)

If you want to use numbers, make **ALL** results numbers:

```sql
SELECT 
    order_id,
    sales,
    CASE
        WHEN sales > 50 THEN 3
        WHEN sales > 20 THEN 2
        ELSE 1
    END AS priority_level
FROM sales_orders;
```

**Output:**
```
order_id | sales | priority_level
---------|-------|---------------
1        | 15    | 1
2        | 60    | 3
3        | 35    | 2
4        | 50    | 2
5        | 75    | 3
```

✅ **Why it works:** All results (3, 2, 1) are **numbers**.

---

## Quick Practice Examples

### Example 1: Grading System (Strings)

```sql
SELECT 
    student_name,
    test_score,
    CASE
        WHEN test_score >= 90 THEN 'A'
        WHEN test_score >= 80 THEN 'B'
        WHEN test_score >= 70 THEN 'C'
        ELSE 'F'
    END AS letter_grade
FROM student_scores;
```

**Sample Output:**
```
student_name | test_score | letter_grade
-------------|------------|-------------
Sarah        | 95         | A
Mike         | 82         | B
John         | 65         | F
Emma         | 88         | B
```

---

### Example 2: Priority Numbers (Integers)

```sql
SELECT 
    task_name,
    urgency_level,
    CASE
        WHEN urgency_level = 'Critical' THEN 1
        WHEN urgency_level = 'High' THEN 2
        WHEN urgency_level = 'Medium' THEN 3
        ELSE 4
    END AS priority_number
FROM tasks;
```

**Sample Output:**
```
task_name      | urgency_level | priority_number
---------------|---------------|----------------
Fix Bug        | Critical      | 1
Add Feature    | High          | 2
Update Docs    | Low           | 4
Test Code      | Medium        | 3
```

---

## Where Can You Use CASE Statements?

Great news! You can use CASE statements **EVERYWHERE** in SQL:

- ✅ In **SELECT** (choosing columns)
- ✅ In **WHERE** (filtering data)
- ✅ In **ORDER BY** (sorting data)
- ✅ In **GROUP BY** (grouping data)
- ✅ In **JOIN** conditions
- ✅ Basically **ANYWHERE**!

**Example in WHERE clause:**
```sql
SELECT *
FROM sales_orders
WHERE 
    CASE
        WHEN sales > 50 THEN 1
        ELSE 0
    END = 1;
```

This finds all orders where sales > 50.

---

## Summary: The One Rule to Remember

📌 **All results in your CASE statement must be the SAME data type:**
- If you use text (strings), use text everywhere
- If you use numbers, use numbers everywhere
- Don't mix them up!

Think of it like this: If you're making a list of colors, don't suddenly write a number. Keep everything consistent!
