# SQL Subquery Categories - Simple Guide

## Overview: Different Types of Subqueries

Just like ice cream comes in different flavors, subqueries come in different types! Let's explore them all.

---

## Category 1: By Dependency (How They Work Together)

### Type 1: Non-Correlated Subquery (Independent) 🆓

The subquery works **alone** - it doesn't need information from the main query.

**Think of it like:** Doing your homework without asking your parents for help!

```sql
-- The subquery runs independently
SELECT student_name, score
FROM students
WHERE score > (
    -- This subquery doesn't need anything from the main query
    SELECT AVG(score) FROM students
);
```

**Expected Output:**
```
student_name | score
-------------|-------
Alice        | 95
Charlie      | 88
Emma         | 92
```

### Type 2: Correlated Subquery (Dependent) 🔗

The subquery **needs** information from the main query to work.

**Think of it like:** Asking your parent "What's for dinner?" - you need them to answer!

```sql
-- The subquery uses data from the main query
SELECT 
    s1.student_name,
    s1.grade,
    s1.score
FROM students s1
WHERE score > (
    -- This subquery depends on s1.grade from main query
    SELECT AVG(score)
    FROM students s2
    WHERE s2.grade = s1.grade  -- Uses main query's grade!
);
```

**Expected Output:**
```
student_name | grade | score
-------------|-------|-------
Alice        | 7     | 95
Bob          | 8     | 90
Charlie      | 7     | 88
```

---

## Category 2: By Result Type (What They Return)

### Type 1: Scalar Subquery (Returns ONE Value) 1️⃣

Returns just **one single value** - like one number or one word.

**Think of it like:** Asking "What's the highest score?" - you get ONE answer!

```sql
-- Returns a single value: the average
SELECT 
    product_name,
    price,
    (SELECT AVG(price) FROM products) AS average_price
FROM products;
```

**Expected Output:**
```
product_name | price | average_price
-------------|-------|---------------
Laptop       | 800   | 350
Mouse        | 25    | 350
Keyboard     | 75    | 350
Monitor      | 300   | 350
```

### Type 2: Row Subquery (Returns ONE Row) ➡️

Returns **one row** with multiple columns.

**Think of it like:** Getting one student's complete report card.

```sql
-- Returns one row with multiple values
SELECT *
FROM students
WHERE (grade, score) = (
    SELECT grade, MAX(score)
    FROM students
    WHERE grade = 7
);
```

**Expected Output:**
```
student_id | student_name | grade | score
-----------|--------------|-------|-------
5          | Alice        | 7     | 95
```

### Type 3: Table Subquery (Returns Multiple Rows & Columns) 📊

Returns a **full table** with many rows and columns.

**Think of it like:** Getting the entire class's report cards!

```sql
-- Returns multiple rows and columns
SELECT *
FROM (
    -- This subquery returns a whole table
    SELECT student_name, grade, score
    FROM students
    WHERE score >= 80
) AS high_scorers
WHERE grade = 7;
```

**Expected Output:**
```
student_name | grade | score
-------------|-------|-------
Alice        | 7     | 95
Charlie      | 7     | 88
David        | 7     | 82
```

---

## Category 3: By Location (Where You Use Them)

### Location 1: In SELECT Clause

Used to calculate values for each row.

```sql
SELECT 
    student_name,
    score,
    (SELECT AVG(score) FROM students) AS class_average,
    score - (SELECT AVG(score) FROM students) AS difference
FROM students;
```

**Expected Output:**
```
student_name | score | class_average | difference
-------------|-------|---------------|------------
Alice        | 95    | 78            | 17
Bob          | 72    | 78            | -6
Charlie      | 88    | 78            | 10
```

### Location 2: In FROM Clause (Most Common!) ⭐

Treat the subquery result as a temporary table.

```sql
SELECT 
    grade,
    AVG(score) AS average_score
FROM (
    -- Create a temporary table of high scorers
    SELECT * 
    FROM students 
    WHERE score >= 70
) AS passing_students
GROUP BY grade;
```

**Expected Output:**
```
grade | average_score
------|---------------
6     | 82
7     | 87
8     | 85
```

### Location 3: In JOIN

Use a subquery as a table to join with.

```sql
SELECT 
    s.student_name,
    s.score,
    avg_scores.grade_average
FROM students s
JOIN (
    -- Subquery calculating averages
    SELECT grade, AVG(score) AS grade_average
    FROM students
    GROUP BY grade
) AS avg_scores ON s.grade = avg_scores.grade;
```

**Expected Output:**
```
student_name | score | grade_average
-------------|-------|---------------
Alice        | 95    | 87
Bob          | 88    | 87
Charlie      | 90    | 83
Diana        | 76    | 83
```

### Location 4: In WHERE Clause with Comparison Operators

Use `=`, `>`, `<`, `>=`, `<=`, `!=`

```sql
-- Find products more expensive than average
SELECT product_name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

**Expected Output:**
```
product_name | price
-------------|-------
Laptop       | 800
Tablet       | 400
Smartphone   | 600
```

### Location 5: In WHERE Clause with Logical Operators

#### Using IN (check if value is in a list)

```sql
-- Find students who have overdue library books
SELECT student_name
FROM students
WHERE student_id IN (
    SELECT student_id 
    FROM library_books 
    WHERE return_date < CURRENT_DATE
);
```

**Expected Output:**
```
student_name
-------------
Bob
Charlie
Emma
```

#### Using ANY (compare to any value in the list)

```sql
-- Find products cheaper than ANY premium product
SELECT product_name, price
FROM products
WHERE price < ANY (
    SELECT price 
    FROM products 
    WHERE category = 'Premium'
);
```

**Expected Output:**
```
product_name | price
-------------|-------
Mouse        | 25
Keyboard     | 75
Webcam       | 100
```

#### Using ALL (compare to all values in the list)

```sql
-- Find students who scored better than ALL grade 6 students
SELECT student_name, score
FROM students
WHERE score > ALL (
    SELECT score 
    FROM students 
    WHERE grade = 6
);
```

**Expected Output:**
```
student_name | score
-------------|-------
Alice        | 95
Emma         | 92
```

#### Using EXISTS (check if subquery returns any rows)

```sql
-- Find students who have at least one library book
SELECT student_name
FROM students s
WHERE EXISTS (
    SELECT 1 
    FROM library_books lb 
    WHERE lb.student_id = s.student_id
);
```

**Expected Output:**
```
student_name
-------------
Alice
Bob
Charlie
Diana
```

---

## Quick Reference Chart

| **Category** | **Types** | **Example** |
|--------------|-----------|-------------|
| **Dependency** | Non-Correlated, Correlated | Independent vs. Dependent |
| **Result Type** | Scalar (1 value), Row (1 row), Table (many rows) | One number vs. Full table |
| **Location** | SELECT, FROM, JOIN, WHERE | Where you put it in query |

---

## Summary: All Categories Together

### Example Combining Multiple Concepts

```sql
-- Location: FROM (table subquery)
-- Result Type: Table (multiple rows and columns)
-- Dependency: Non-correlated (independent)

SELECT 
    grade,
    COUNT(*) as high_achievers
FROM (
    -- Subquery returns a table of students above average
    SELECT student_name, grade, score
    FROM students
    WHERE score > (
        -- Scalar subquery returning one value
        SELECT AVG(score) FROM students
    )
) AS above_average_students
GROUP BY grade;
```

**Expected Output:**
```
grade | high_achievers
------|---------------
6     | 8
7     | 12
8     | 10
```

---

## Key Takeaway

Subqueries are **flexible tools** that can:
- Work independently or depend on the main query
- Return one value, one row, or many rows
- Be used in different parts of your SQL query

Master these categories, and you'll be a subquery expert! 🎓
