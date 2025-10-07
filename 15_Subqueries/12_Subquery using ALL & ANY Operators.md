# SQL Subquery with ANY and ALL Operators - Simple Guide

## What are ANY and ALL Operators?

These operators help you compare a value against **multiple values** in a list, but with different rules!

**Think of it like:**
- **ANY** = "Beat at least ONE person in a race" (easier!)
- **ALL** = "Beat EVERYONE in a race" (harder!)

---

## Basic Syntax

```sql
-- Using ANY
SELECT columns
FROM table
WHERE column > ANY (
    SELECT column FROM another_table
);

-- Using ALL
SELECT columns
FROM table
WHERE column > ALL (
    SELECT column FROM another_table
);
```

**Key Difference:**
- **ANY** = Condition must be true for **at least ONE** value
- **ALL** = Condition must be true for **EVERY** value

---

## Understanding ANY Operator

**ANY means:** "True if comparison works with AT LEAST ONE value in the list"

### Example: Is 75 greater than ANY of these numbers? [20, 50, 90]
- 75 > 20? ✅ YES
- 75 > 50? ✅ YES
- 75 > 90? ❌ NO

**Result:** TRUE! (Because 75 is greater than at least one number)

---

## Understanding ALL Operator

**ALL means:** "True if comparison works with EVERY value in the list"

### Example: Is 75 greater than ALL of these numbers? [20, 50, 90]
- 75 > 20? ✅ YES
- 75 > 50? ✅ YES
- 75 > 90? ❌ NO (Failed here!)

**Result:** FALSE! (Because 75 is NOT greater than ALL numbers)

---

## Example 1: Female Employees Earning More Than ANY Male

**Task:** Find female employees whose salary is higher than at least one male employee.

### Step 1: See All Employees

```sql
SELECT 
    employee_id,
    first_name,
    gender,
    salary
FROM employees;
```

**Output:**
```
employee_id | first_name | gender | salary
------------|------------|--------|--------
1           | Michael    | M      | 8000
2           | Frank      | M      | 5000
3           | Kevin      | M      | 4500
4           | Mary       | F      | 6000
5           | Carol      | F      | 4000
```

### Step 2: Filter Female Employees (Main Query)

```sql
SELECT 
    employee_id,
    first_name,
    gender,
    salary
FROM employees
WHERE gender = 'F';
```

**Output:**
```
employee_id | first_name | gender | salary
------------|------------|--------|--------
4           | Mary       | F      | 6000
5           | Carol      | F      | 4000
```

### Step 3: Get Male Salaries (Subquery)

```sql
SELECT salary
FROM employees
WHERE gender = 'M';
```

**Output:**
```
salary
-------
8000
5000
4500
```

### Step 4: Complete Query with ANY

```sql
SELECT 
    employee_id,
    first_name,
    gender,
    salary
FROM employees
WHERE gender = 'F'
AND salary > ANY (
    -- Subquery: Get all male salaries
    SELECT salary
    FROM employees
    WHERE gender = 'M'
);
```

**How it works:**
- Mary's salary (6000): Is 6000 > ANY of [8000, 5000, 4500]?
  - 6000 > 8000? NO
  - 6000 > 5000? ✅ YES! (Match found!)
  - Result: Include Mary ✓

- Carol's salary (4000): Is 4000 > ANY of [8000, 5000, 4500]?
  - 4000 > 8000? NO
  - 4000 > 5000? NO
  - 4000 > 4500? NO
  - Result: Exclude Carol ✗

**Expected Output:**
```
employee_id | first_name | gender | salary
------------|------------|--------|--------
4           | Mary       | F      | 6000
```

---

## Example 2: Female Employees Earning More Than ALL Males

**Task:** Find female employees whose salary is higher than EVERY male employee.

```sql
SELECT 
    employee_id,
    first_name,
    gender,
    salary
FROM employees
WHERE gender = 'F'
AND salary > ALL (
    -- Subquery: Get all male salaries
    SELECT salary
    FROM employees
    WHERE gender = 'M'
);
```

**How it works:**
- Mary's salary (6000): Is 6000 > ALL of [8000, 5000, 4500]?
  - 6000 > 8000? ❌ NO! (Failed!)
  - Result: Exclude Mary ✗

- Carol's salary (4000): Is 4000 > ALL of [8000, 5000, 4500]?
  - 4000 > 8000? ❌ NO!
  - Result: Exclude Carol ✗

**Expected Output:**
```
(No rows)
```

**Why empty?** No female employee earns more than ALL male employees!

---

## Example 3: Students Scoring Better Than ANY Grade 6 Student

**Task:** Find grade 7 students who scored better than at least one grade 6 student.

```sql
SELECT 
    student_name,
    grade,
    score
FROM students
WHERE grade = 7
AND score > ANY (
    SELECT score
    FROM students
    WHERE grade = 6
);
```

**Subquery returns:** [65, 70, 72, 80]

**Expected Output:**
```
student_name | grade | score
-------------|-------|-------
Alice        | 7     | 95   (95 > 65 ✓)
Bob          | 7     | 88   (88 > 65 ✓)
Charlie      | 7     | 78   (78 > 65 ✓)
Diana        | 7     | 68   (68 > 65 ✓)
```

All grade 7 students score better than at least one grade 6 student!

---

## Example 4: Students Scoring Better Than ALL Grade 6 Students

**Task:** Find grade 7 students who scored better than EVERY grade 6 student.

```sql
SELECT 
    student_name,
    grade,
    score
FROM students
WHERE grade = 7
AND score > ALL (
    SELECT score
    FROM students
    WHERE grade = 6
);
```

**Subquery returns:** [65, 70, 72, 80]
**Highest grade 6 score:** 80

**Expected Output:**
```
student_name | grade | score
-------------|-------|-------
Alice        | 7     | 95   (95 > 80 ✓)
Bob          | 7     | 88   (88 > 80 ✓)
```

Only students who beat the BEST grade 6 student!

---

## Comparison Operators with ANY and ALL

You can use ANY and ALL with different operators:

### With Greater Than (>)

```sql
-- Greater than ANY (easier to match)
WHERE salary > ANY (subquery)  -- Beat at least one

-- Greater than ALL (harder to match)
WHERE salary > ALL (subquery)  -- Beat everyone
```

### With Less Than (<)

```sql
-- Products cheaper than ANY competitor
SELECT product_name, price
FROM our_products
WHERE price < ANY (
    SELECT price FROM competitor_products
);

-- Products cheaper than ALL competitors
SELECT product_name, price
FROM our_products
WHERE price < ALL (
    SELECT price FROM competitor_products
);
```

### With Equal (=)

```sql
-- = ANY is the same as IN!
WHERE product_id = ANY (SELECT product_id FROM orders)
-- Same as:
WHERE product_id IN (SELECT product_id FROM orders)
```

---

## Real-World Examples

### Example 5: Books More Popular Than Any Classic

```sql
SELECT 
    book_title,
    times_borrowed
FROM books
WHERE publication_year > 2000
AND times_borrowed > ANY (
    SELECT times_borrowed
    FROM books
    WHERE publication_year < 1950  -- Classic books
);
```

**Expected Output:**
```
book_title              | times_borrowed
------------------------|----------------
Harry Potter            | 45
Diary of a Wimpy Kid    | 52
Percy Jackson           | 38
```

### Example 6: Products Cheaper Than All Premium Products

```sql
SELECT 
    product_name,
    price,
    category
FROM products
WHERE category = 'Budget'
AND price < ALL (
    SELECT price
    FROM products
    WHERE category = 'Premium'
);
```

**Expected Output:**
```
product_name      | price | category
------------------|-------|----------
Basic Mouse       | 15    | Budget
Simple Keyboard   | 25    | Budget
Wired Headphones  | 20    | Budget
```

---

## Visual Comparison

### ANY Operator (At least one match needed)

```
Your value: 60

List to compare: [20, 50, 80]

60 > 20? ✅ YES → STOP! Condition TRUE!
(Don't need to check others)

Result: INCLUDE this row
```

### ALL Operator (Every match needed)

```
Your value: 60

List to compare: [20, 50, 80]

60 > 20? ✅ YES
60 > 50? ✅ YES
60 > 80? ❌ NO → STOP! Condition FALSE!

Result: EXCLUDE this row
```

---

## Quick Reference Table

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `> ANY` | Greater than at least one | 60 > ANY [20, 50, 80] | TRUE |
| `> ALL` | Greater than every value | 60 > ALL [20, 50, 80] | FALSE |
| `< ANY` | Less than at least one | 60 < ANY [20, 50, 80] | TRUE |
| `< ALL` | Less than every value | 60 < ALL [20, 50, 80] | FALSE |
| `= ANY` | Equal to at least one (same as IN) | 60 = ANY [20, 60, 80] | TRUE |
| `= ALL` | Equal to all (rarely used) | 60 = ALL [60, 60, 60] | TRUE |

---

## Common Mistake ⚠️

### Using Comparison Operator Alone (Wrong!)

```sql
-- ❌ ERROR! Subquery returns multiple values
SELECT *
FROM employees
WHERE salary > (
    SELECT salary FROM employees WHERE gender = 'M'
);
-- Error: Subquery returned more than one value!

-- ✅ CORRECT! Use ANY or ALL
SELECT *
FROM employees
WHERE salary > ANY (
    SELECT salary FROM employees WHERE gender = 'M'
);
```

---

## Key Takeaways

✅ **ANY = At least ONE match** (easier condition)  
✅ **ALL = EVERY match** (stricter condition)  
✅ **Use with comparison operators:** >, <, >=, <=, =, !=  
✅ **= ANY is same as IN operator**  
✅ **Subquery can return MULTIPLE values**  
✅ **ANY stops checking after first match**  
✅ **ALL checks every single value**

**Memory Trick:**
- **ANY** = "**A**t least one" (both start with A!)
- **ALL** = "**A**bsolutely **L**iterally **L**iterally everyone!"

Think of ANY as a friendly test (pass if you beat one person) and ALL as a championship (beat everyone to win)! 🏆🎯
