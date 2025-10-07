# SQL GROUP BY Clause - Combining and Aggregating Data 📦

## What is GROUP BY?

**GROUP BY** is like putting similar items into boxes!

**Think of it like this:**
- You have marbles of different colors (red, blue, green)
- You want to put all RED marbles in one box, all BLUE in another
- GROUP BY does this with data - it combines rows that have the same value!

**Main Purpose:** To **combine rows** and calculate **totals, averages, or counts** for each group.

---

## What Does "Aggregate" Mean? 🧮

**Aggregate** = Combine multiple values into ONE summary value

Common aggregate functions:
- **SUM()** - Add up all values
- **COUNT()** - Count how many rows
- **AVG()** - Calculate average
- **MAX()** - Find the highest value
- **MIN()** - Find the lowest value

---

## GROUP BY Syntax 📝

```sql
SELECT category_column, AGGREGATE_FUNCTION(value_column)
FROM table_name
GROUP BY category_column;
```

**Structure:**
1. **SELECT** - The category column + aggregate function
2. **FROM** - Which table
3. **GROUP BY** - Which column to group by

---

## How SQL Executes GROUP BY 🔄

### Execution Order:

```
Step 1: FROM → Get all data from table
Step 2: GROUP BY → Combine rows with same values
Step 3: Aggregate functions → Calculate totals/counts
Step 4: SELECT → Display results
```

---

## Example 1: Total Score for Each Country

**Task:** Find the total score for each country.

```sql
SELECT country, SUM(score) AS total_score
FROM customers
GROUP BY country;
```

**Original Data (customers table):**
```
customer_id | first_name | country  | score
------------|-----------|----------|-------
1           | Mario     | Germany  | 350
2           | Martin    | Germany  | 500
3           | John      | UK       | 750
4           | Priya     | USA      | 900
5           | Lisa      | USA      | 0
```

### Step-by-Step Process:

**Step 1:** Get all data (5 rows)

**Step 2:** Group by country
```
Germany: Mario (350) + Martin (500)
UK: John (750)
USA: Priya (900) + Lisa (0)
```

**Step 3:** Calculate SUM for each group
```
Germany: 350 + 500 = 850
UK: 750
USA: 900 + 0 = 900
```

**Expected Output (3 rows):**
```
country  | total_score
---------|------------
Germany  | 850
UK       | 750
USA      | 900
```

**Magic!** 5 rows became 3 rows! Each country appears only ONCE with its total score.

---

## Understanding AS (Aliases) 🏷️

**AS** gives a column a nickname (temporary name) for the output.

```sql
SELECT country, SUM(score) AS total_score
FROM customers
GROUP BY country;
```

**Without AS:**
```
country  | (no column name)
---------|------------------
Germany  | 850
```

**With AS:**
```
country  | total_score
---------|------------
Germany  | 850
```

**Important:** The alias only exists in THIS query! It doesn't change the database.

---

## Example 2: Counting Students per Class

**Task:** How many students are in each class?

```sql
SELECT class, COUNT(student_id) AS total_students
FROM students
GROUP BY class;
```

**Sample Data:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
1          | Ravi    | 7A    | 85
2          | Meera   | 7B    | 90
3          | Amit    | 7A    | 78
4          | Priya   | 7B    | 88
5          | Karan   | 7A    | 92
6          | Sneha   | 7C    | 85
```

**Expected Output:**
```
class | total_students
------|---------------
7A    | 3
7B    | 2
7C    | 1
```

**What happened?**
- 7A has 3 students (Ravi, Amit, Karan)
- 7B has 2 students (Meera, Priya)
- 7C has 1 student (Sneha)

---

## Example 3: Multiple Aggregations

**Task:** Find the total score AND total number of customers for each country.

```sql
SELECT 
    country,
    SUM(score) AS total_score,
    COUNT(customer_id) AS total_customers
FROM customers
GROUP BY country;
```

**Sample Data:**
```
customer_id | first_name | country  | score
------------|-----------|----------|-------
1           | Mario     | Germany  | 350
2           | Martin    | Germany  | 500
3           | John      | UK       | 750
4           | Priya     | USA      | 900
5           | Lisa      | USA      | 0
```

**Expected Output:**
```
country  | total_score | total_customers
---------|-------------|----------------
Germany  | 850         | 2
UK       | 750         | 1
USA      | 900         | 2
```

**What happened?**
- Germany: 2 customers, total score = 850
- UK: 1 customer, total score = 750
- USA: 2 customers, total score = 900

---

## Example 4: Average Marks per Class

**Task:** Find the average marks for each class.

```sql
SELECT 
    class,
    AVG(marks) AS average_marks
FROM students
GROUP BY class;
```

**Sample Data:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
1          | Ravi    | 7A    | 80
2          | Meera   | 7B    | 90
3          | Amit    | 7A    | 70
4          | Priya   | 7B    | 85
```

**Expected Output:**
```
class | average_marks
------|---------------
7A    | 75.00
7B    | 87.50
```

**Calculation:**
- 7A: (80 + 70) ÷ 2 = 75
- 7B: (90 + 85) ÷ 2 = 87.5

---

## Example 5: Highest and Lowest Sales by Product

**Task:** Find the highest and lowest sale amount for each product.

```sql
SELECT 
    product_name,
    MAX(sale_amount) AS highest_sale,
    MIN(sale_amount) AS lowest_sale
FROM sales
GROUP BY product_name;
```

**Sample Data:**
```
sale_id | product_name | sale_amount
--------|-------------|-------------
1       | Laptop      | 50000
2       | Laptop      | 55000
3       | Phone       | 30000
4       | Phone       | 28000
5       | Laptop      | 52000
```

**Expected Output:**
```
product_name | highest_sale | lowest_sale
-------------|--------------|-------------
Laptop       | 55000        | 50000
Phone        | 30000        | 28000
```

---

## Important Rule: Non-Aggregated Columns! ⚠️

**CRITICAL RULE:** Every column in SELECT must be either:
1. **In the GROUP BY clause**, OR
2. **Inside an aggregate function**

### ❌ Wrong Example:

```sql
SELECT country, first_name, SUM(score)
FROM customers
GROUP BY country;
```

**Error!** `first_name` is neither grouped nor aggregated!

### ✅ Correct Solution 1: Add to GROUP BY

```sql
SELECT country, first_name, SUM(score) AS total_score
FROM customers
GROUP BY country, first_name;
```

**Expected Output:**
```
country  | first_name | total_score
---------|-----------|-------------
Germany  | Mario     | 350
Germany  | Martin    | 500
UK       | John      | 750
USA      | Priya     | 900
USA      | Lisa      | 0
```

**Result:** 5 rows (each person appears separately)

### ✅ Correct Solution 2: Remove from SELECT

```sql
SELECT country, SUM(score) AS total_score
FROM customers
GROUP BY country;
```

**Expected Output:**
```
country  | total_score
---------|------------
Germany  | 850
UK       | 750
USA      | 900
```

**Result:** 3 rows (grouped by country only)

---

## Grouping by Multiple Columns 🎯

When you group by **multiple columns**, you get one row for each **unique combination**.

### Example: Group by Country AND First Name

```sql
SELECT country, first_name, SUM(score) AS total_score
FROM customers
GROUP BY country, first_name;
```

**Result:**
- **5 unique combinations** = 5 rows
- Each person gets their own row

### Example: Group by Country Only

```sql
SELECT country, SUM(score) AS total_score
FROM customers
GROUP BY country;
```

**Result:**
- **3 unique countries** = 3 rows
- People from the same country are combined

---

## More Practice Examples 🏃‍♂️

### Example 6: Total Sales per Customer

**Task:** How much has each customer spent in total?

```sql
SELECT 
    customer_name,
    SUM(order_amount) AS total_spent
FROM orders
GROUP BY customer_name;
```

**Sample Data:**
```
order_id | customer_name | order_amount
---------|--------------|-------------
1        | Ravi         | 1000
2        | Priya        | 1500
3        | Ravi         | 2000
4        | Priya        | 500
```

**Expected Output:**
```
customer_name | total_spent
--------------|------------
Ravi          | 3000
Priya         | 2000
```

---

### Example 7: Books per Author

**Task:** How many books has each author written?

```sql
SELECT 
    author,
    COUNT(book_id) AS total_books
FROM books
GROUP BY author;
```

**Sample Data:**
```
book_id | title              | author
--------|-------------------|----------------
1       | Harry Potter 1    | J.K. Rowling
2       | Harry Potter 2    | J.K. Rowling
3       | Matilda           | Roald Dahl
4       | BFG               | Roald Dahl
5       | Charlie Factory   | Roald Dahl
```

**Expected Output:**
```
author        | total_books
--------------|-------------
J.K. Rowling  | 2
Roald Dahl    | 3
```

---

### Example 8: Student Performance Summary

**Task:** For each class, find total students, average marks, and highest marks.

```sql
SELECT 
    class,
    COUNT(student_id) AS total_students,
    AVG(marks) AS average_marks,
    MAX(marks) AS highest_marks
FROM students
GROUP BY class;
```

**Sample Data:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
1          | Ravi    | 7A    | 85
2          | Amit    | 7A    | 92
3          | Meera   | 7B    | 78
4          | Priya   | 7B    | 88
5          | Karan   | 7B    | 90
```

**Expected Output:**
```
class | total_students | average_marks | highest_marks
------|----------------|---------------|---------------
7A    | 2              | 88.50         | 92
7B    | 3              | 85.33         | 90
```

---

## Common Aggregate Functions Summary 📊

| Function | What It Does | Example |
|----------|-------------|---------|
| **SUM()** | Adds all values | `SUM(score)` → 850 |
| **COUNT()** | Counts rows | `COUNT(student_id)` → 5 |
| **AVG()** | Calculates average | `AVG(marks)` → 85.5 |
| **MAX()** | Finds highest value | `MAX(price)` → 500 |
| **MIN()** | Finds lowest value | `MIN(price)` → 50 |

---

## Quick Reference Chart 📋

| Want to Find | SQL Code |
|--------------|----------|
| Total per category | `SELECT category, SUM(value) FROM table GROUP BY category;` |
| Count per category | `SELECT category, COUNT(*) FROM table GROUP BY category;` |
| Average per category | `SELECT category, AVG(value) FROM table GROUP BY category;` |
| Multiple summaries | Add more aggregate functions in SELECT |

---

## Key Takeaways 🌟

1. **GROUP BY combines rows** with the same value
2. **Each unique value appears only ONCE** in results
3. **Use aggregate functions** to calculate totals, averages, counts
4. **AS creates aliases** (temporary names) for columns
5. **Columns in SELECT must be** in GROUP BY or aggregated
6. **Multiple columns in GROUP BY** = combinations of values
7. **Reduces number of rows** in output (5 rows → 3 rows)

---

## Complete Query Structure 🏗️

```sql
SELECT column1, AGGREGATE(column2) AS alias   -- Step 4: Display
FROM table_name                               -- Step 1: Get data
WHERE condition                               -- Step 2: Filter rows
GROUP BY column1                              -- Step 3: Group & aggregate
ORDER BY column1;                             -- Step 5: Sort results
```

---

## What's Next? 🚀

You've learned:
- ✅ SELECT (choose columns)
- ✅ FROM (choose table)
- ✅ WHERE (filter rows)
- ✅ ORDER BY (sort results)
- ✅ GROUP BY (combine & aggregate)

**Next topic:** HAVING clause - to filter GROUPS after aggregation! 

GROUP BY creates groups, HAVING filters those groups! 🎯
