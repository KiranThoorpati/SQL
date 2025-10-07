# SQL Correlated Subquery - Simple Guide

## What is a Correlated Subquery?

A **correlated subquery** is a subquery that **depends on the main query**. It's like asking a different question for each student in class!

**Two Types of Subqueries:**

### 1. Non-Correlated Subquery (Independent) 🆓
- Runs **once** by itself
- **Doesn't need** the main query
- Like asking "What's the class average?" - one answer for everyone

### 2. Correlated Subquery (Dependent) 🔗
- Runs **many times** (once per row!)
- **Needs** data from the main query
- Like asking "Is this student above their grade's average?" - different for each grade

---

## How Correlated Subquery Works (Step-by-Step)

### The Process:

```
Step 1: Main query gets FIRST row
   ↓
Step 2: Pass row data to subquery
   ↓
Step 3: Subquery executes with that row's data
   ↓
Step 4: Check if subquery returns result
   ↓
Step 5a: If YES → Include row in output ✓
Step 5b: If NO → Exclude row from output ✗
   ↓
Step 6: Repeat for NEXT row
   ↓
Continue until ALL rows processed
```

**Key Point:** Subquery executes **once for EACH row** in the main query!

---

## Visual Comparison

### Non-Correlated (Runs Once)

```
Main Query: "Get students"
   ↓
Subquery: "Calculate average" → Result: 75
   ↓
Main Query: "Filter using 75"
   ↓
Final Output
```

### Correlated (Runs Multiple Times)

```
Row 1: Alice (Grade 7)
   ↓
Subquery: "Grade 7 average?" → 80
   ↓
Check: 95 > 80? YES ✓

Row 2: Bob (Grade 8)
   ↓
Subquery: "Grade 8 average?" → 75
   ↓
Check: 72 > 75? NO ✗

Row 3: Charlie (Grade 7)
   ↓
Subquery: "Grade 7 average?" → 80
   ↓
Check: 88 > 80? YES ✓
```

---

## Example 1: Customer Details with Order Count

**Task:** Show all customers with how many orders each one made.

### Non-Correlated Approach (We learned before)

```sql
-- This won't work correctly for individual customers
SELECT 
    customer_id,
    customer_name,
    (SELECT COUNT(*) FROM orders) AS total_orders
FROM customers;
```

**Problem:** Shows TOTAL orders for ALL customers (same number repeated)

**Wrong Output:**
```
customer_id | customer_name | total_orders
------------|---------------|-------------
1           | John Smith    | 10  ← Same for all!
2           | Mary Jones    | 10  ← Same for all!
3           | Bob Wilson    | 10  ← Same for all!
```

### Correlated Approach (Correct!)

```sql
-- Main Query
SELECT 
    c.customer_id,
    c.customer_name,
    c.city,
    (
        -- Correlated Subquery: Depends on main query!
        SELECT COUNT(*)
        FROM orders o
        WHERE o.customer_id = c.customer_id  -- Uses main query data!
    ) AS total_orders
FROM customers c;
```

**Expected Output:**
```
customer_id | customer_name | city      | total_orders
------------|---------------|-----------|-------------
1           | John Smith    | New York  | 3
2           | Mary Jones    | Chicago   | 2
3           | Bob Wilson    | Boston    | 4
4           | Alice Brown   | Seattle   | 1
5           | Anna Davis    | Miami     | 0
```

**What Happens:**
- **Row 1 (John):** Subquery runs: `COUNT(*) WHERE customer_id = 1` → Result: 3
- **Row 2 (Mary):** Subquery runs: `COUNT(*) WHERE customer_id = 2` → Result: 2
- **Row 3 (Bob):** Subquery runs: `COUNT(*) WHERE customer_id = 3` → Result: 4
- And so on...

---

## Example 2: Students Above Their Grade Average

**Task:** Find students who scored above their own grade's average.

```sql
SELECT 
    s1.student_name,
    s1.grade,
    s1.score,
    (
        -- Correlated: Calculate average for THIS student's grade
        SELECT AVG(score)
        FROM students s2
        WHERE s2.grade = s1.grade  -- Depends on main query grade!
    ) AS grade_average
FROM students s1
WHERE s1.score > (
    SELECT AVG(score)
    FROM students s2
    WHERE s2.grade = s1.grade
);
```

**Expected Output:**
```
student_name | grade | score | grade_average
-------------|-------|-------|---------------
Alice        | 7     | 95    | 80
Charlie      | 7     | 88    | 80
Emma         | 8     | 90    | 82
```

**What Happens:**
- **Alice (Grade 7):** Subquery calculates Grade 7 average → 80 → 95 > 80? YES ✓
- **Bob (Grade 7):** Subquery calculates Grade 7 average → 80 → 72 > 80? NO ✗
- **Emma (Grade 8):** Subquery calculates Grade 8 average → 82 → 90 > 82? YES ✓

---

## Example 3: Products Sold More Than Category Average

**Task:** Find products sold more than their category's average.

```sql
SELECT 
    p.product_name,
    p.category,
    p.units_sold,
    (
        -- Correlated: Average for THIS product's category
        SELECT AVG(units_sold)
        FROM products p2
        WHERE p2.category = p.category
    ) AS category_average
FROM products p
WHERE p.units_sold > (
    SELECT AVG(units_sold)
    FROM products p2
    WHERE p2.category = p.category
);
```

**Expected Output:**
```
product_name | category    | units_sold | category_average
-------------|-------------|------------|------------------
Laptop       | Electronics | 150        | 100
Gaming Mouse | Accessories | 250        | 180
Keyboard Pro | Accessories | 220        | 180
```

---

## Example 4: Employees Earning Above Department Average

**Task:** Find employees earning more than their department's average salary.

```sql
SELECT 
    e1.employee_name,
    e1.department,
    e1.salary,
    (
        -- Correlated: Average for THIS employee's department
        SELECT AVG(salary)
        FROM employees e2
        WHERE e2.department = e1.department
    ) AS dept_average
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.department = e1.department
);
```

**Expected Output:**
```
employee_name | department | salary | dept_average
--------------|------------|--------|-------------
John Smith    | Sales      | 65000  | 55000
Mary Johnson  | Marketing  | 58000  | 52000
Bob Wilson    | Sales      | 70000  | 55000
```

---

## Key Differences: Non-Correlated vs Correlated

| Feature | Non-Correlated | Correlated |
|---------|----------------|------------|
| **Independence** | Independent | Dependent on main query |
| **Executions** | Runs ONCE | Runs MANY times (per row) |
| **Can run alone?** | ✅ YES | ❌ NO (needs main query) |
| **Difficulty** | Easier to write/read | Harder, more complex |
| **Performance** | FASTER ⚡ | SLOWER 🐌 |
| **Use case** | Static comparison | Row-by-row comparison |
| **Result** | Same value for all rows | Different value per row |

---

## Testing Correlated vs Non-Correlated

### Non-Correlated (Can test alone)

```sql
-- ✅ This works by itself
SELECT AVG(score) FROM students;
```

**Output:** `78.5`

### Correlated (Cannot test alone)

```sql
-- ❌ This gives error if run alone
SELECT AVG(score)
FROM students s2
WHERE s2.grade = s1.grade;  -- Who is s1? Error!
```

**Must run with main query!**

---

## Real-World Example: Library System

**Task:** Find books borrowed more times than average for their category.

```sql
SELECT 
    b1.book_title,
    b1.category,
    (
        -- Count borrows for THIS book
        SELECT COUNT(*)
        FROM library_transactions lt
        WHERE lt.book_id = b1.book_id
    ) AS times_borrowed,
    (
        -- Average borrows for THIS book's category
        SELECT AVG(borrow_count)
        FROM (
            SELECT COUNT(*) AS borrow_count
            FROM library_transactions lt2
            JOIN books b2 ON lt2.book_id = b2.book_id
            WHERE b2.category = b1.category
            GROUP BY lt2.book_id
        ) AS category_stats
    ) AS category_average
FROM books b1
WHERE (
    SELECT COUNT(*)
    FROM library_transactions lt
    WHERE lt.book_id = b1.book_id
) > (
    SELECT AVG(borrow_count)
    FROM (
        SELECT COUNT(*) AS borrow_count
        FROM library_transactions lt2
        JOIN books b2 ON lt2.book_id = b2.book_id
        WHERE b2.category = b1.category
        GROUP BY lt2.book_id
    ) AS category_stats
);
```

**Expected Output:**
```
book_title           | category | times_borrowed | category_average
---------------------|----------|----------------|------------------
Harry Potter         | Fantasy  | 45             | 30
The Hobbit          | Fantasy  | 38             | 30
Diary of a Wimpy Kid | Fiction  | 52             | 35
```

---

## When to Use Each Type

### Use Non-Correlated When:
✅ Need same comparison value for all rows  
✅ Want better performance  
✅ Comparing to overall average/max/min  

### Use Correlated When:
✅ Need different comparison for each row  
✅ Comparing within groups (by grade, department, category)  
✅ Need row-specific calculations  

---

## Key Takeaways

✅ **Correlated = Depends on main query**  
✅ **Runs ONCE PER ROW** (slower but more flexible)  
✅ **Cannot test alone** (needs main query data)  
✅ **Uses aliases to connect** main and subquery  
✅ **Perfect for row-by-row comparisons**  
✅ **Non-correlated is faster** but less flexible  
✅ **Common pattern:** `WHERE subquery_column = main_query_column`

Think of correlated subqueries like asking a personalized question to each student, while non-correlated is asking one question for the whole class! 🎓🔄
