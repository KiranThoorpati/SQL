# SQL WHERE Clause - Filtering Your Data 🔍

## What is the WHERE Clause?

The **WHERE clause** is like a filter or a strainer! It helps you see only the data you want, based on a condition.

**Think of it like this:**
- Imagine you have a box of colored balls (red, blue, green)
- You only want to see the RED balls
- WHERE clause picks out only the red balls and shows them to you!

### How It Works:
- ✅ Data that **meets the condition** → stays in the results
- ❌ Data that **doesn't meet the condition** → gets filtered out

---

## WHERE Clause Syntax 📝

```sql
SELECT column1, column2
FROM table_name
WHERE condition;
```

**Structure:**
1. **SELECT** - which columns to show
2. **FROM** - which table to use
3. **WHERE** - what condition to apply

---

## How SQL Executes WHERE Clause 🔄

### Execution Order:

```
Step 1: FROM → Get all data from the table
Step 2: WHERE → Check each row against the condition
         ↓
    Does it meet the condition?
         ↓
    YES → Keep the row
    NO → Remove the row
         ↓
Step 3: SELECT → Choose which columns to display
```

---

## Example Walkthrough: Score Filter

Let's see how SQL filters data step by step:

**Original Data (customers table):**
```
customer_id | name    | country  | score
------------|---------|----------|-------
1           | Maria   | USA      | 350
2           | John    | UK       | 600
3           | George  | Germany  | 750
4           | Martin  | Germany  | 400
5           | Lisa    | France   | 0
```

**Condition:** Score must be higher than 500

### Step-by-Step Filtering:

```
Row 1: Maria, score = 350 → 350 > 500? NO → ❌ Remove
Row 2: John, score = 600 → 600 > 500? YES → ✅ Keep
Row 3: George, score = 750 → 750 > 500? YES → ✅ Keep
Row 4: Martin, score = 400 → 400 > 500? NO → ❌ Remove
Row 5: Lisa, score = 0 → 0 > 500? NO → ❌ Remove
```

**Final Result:** Only 2 customers out of 5!

---

## Common Operators in WHERE Clause ⚖️

| Operator | Meaning | Example |
|----------|---------|---------|
| `=` | Equal to | `score = 100` |
| `!=` or `<>` | Not equal to | `score != 0` |
| `>` | Greater than | `score > 500` |
| `<` | Less than | `score < 100` |
| `>=` | Greater than or equal | `score >= 75` |
| `<=` | Less than or equal | `score <= 90` |

---

## Example 1: Filter by Score (Number Condition)

**Task:** Retrieve customers with score not equal to zero.

```sql
SELECT *
FROM customers
WHERE score != 0;
```

**Original Data:**
```
customer_id | first_name | country  | score
------------|-----------|----------|-------
1           | Ravi      | India    | 85
2           | Priya     | USA      | 92
3           | Amit      | UK       | 78
4           | Sneha     | Germany  | 88
5           | Karan     | France   | 0
```

**Expected Output:**
```
customer_id | first_name | country  | score
------------|-----------|----------|-------
1           | Ravi      | India    | 85
2           | Priya     | USA      | 92
3           | Amit      | UK       | 78
4           | Sneha     | Germany  | 88
```

**What happened?** Karan's row was removed because score = 0 (doesn't meet condition!)

---

## Example 2: Filter by Country (Text Condition)

**Task:** Retrieve customers from Germany.

```sql
SELECT *
FROM customers
WHERE country = 'Germany';
```

**Original Data:**
```
customer_id | first_name | country  | score
------------|-----------|----------|-------
1           | Ravi      | India    | 85
2           | Priya     | USA      | 92
3           | Amit      | UK       | 78
4           | Sneha     | Germany  | 88
5           | Karan     | Germany  | 95
```

**Expected Output:**
```
customer_id | first_name | country  | score
------------|-----------|----------|-------
4           | Sneha     | Germany  | 88
5           | Karan     | Germany  | 95
```

**Important:** Only customers from Germany are shown!

---

## 🚨 Critical Rule: Single Quotes for Text! 🚨

When filtering text values, you **MUST** use single quotes:

### ❌ Wrong:
```sql
WHERE country = Germany
```
**Error!** SQL thinks "Germany" is a column name!

### ✅ Correct:
```sql
WHERE country = 'Germany'
```
**Works!** SQL knows "Germany" is a text value!

### Rule Summary:
- **Numbers** → No quotes needed: `WHERE score = 85`
- **Text** → Need single quotes: `WHERE country = 'India'`

---

## Example 3: Combining WHERE with Specific Columns

**Task:** Show only the first name and country of customers from Germany.

```sql
SELECT first_name, country
FROM customers
WHERE country = 'Germany';
```

**Expected Output:**
```
first_name | country
-----------|----------
Sneha      | Germany
Karan      | Germany
```

**Result:** Filtered ROWS (only Germany) AND filtered COLUMNS (only name and country)!

---

## More Practice Examples 🏃‍♂️

### Example 4: Students with High Marks

**Task:** Find students who scored more than 80.

```sql
SELECT *
FROM students
WHERE marks > 80;
```

**Sample Data:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
1          | Rahul   | 7A    | 75
2          | Meera   | 7B    | 85
3          | Arjun   | 7A    | 90
4          | Sita    | 7C    | 78
5          | Karan   | 7B    | 92
```

**Expected Output:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
2          | Meera   | 7B    | 85
3          | Arjun   | 7A    | 90
5          | Karan   | 7B    | 92
```

---

### Example 5: Find Specific Product

**Task:** Show products that cost exactly 500 rupees.

```sql
SELECT product_name, price
FROM products
WHERE price = 500;
```

**Sample Data:**
```
product_id | product_name | price | quantity
-----------|-------------|-------|----------
1          | Notebook    | 50    | 100
2          | Textbook    | 500   | 50
3          | Pencil Box  | 150   | 200
4          | Calculator  | 500   | 30
```

**Expected Output:**
```
product_name | price
-------------|-------
Textbook     | 500
Calculator   | 500
```

---

### Example 6: Books Under Budget

**Task:** Find books that cost less than or equal to 300 rupees.

```sql
SELECT title, price
FROM books
WHERE price <= 300;
```

**Sample Data:**
```
book_id | title           | author        | price
--------|----------------|---------------|-------
1       | Harry Potter   | J.K. Rowling  | 500
2       | Matilda        | Roald Dahl    | 250
3       | Wonder         | R.J. Palacio  | 300
4       | Diary of Wimpy | Jeff Kinney   | 200
```

**Expected Output:**
```
title           | price
----------------|-------
Matilda         | 250
Wonder          | 300
Diary of Wimpy  | 200
```

---

## Example 7: Finding Specific Student

**Task:** Get all information about a student named "Priya".

```sql
SELECT *
FROM students
WHERE name = 'Priya';
```

**Sample Data:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
1          | Rahul   | 7A    | 85
2          | Priya   | 7B    | 92
3          | Meera   | 7C    | 88
```

**Expected Output:**
```
student_id | name    | class | marks
-----------|---------|-------|-------
2          | Priya   | 7B    | 92
```

---

## Common Mistakes to Avoid ❌

### Mistake 1: Forgetting Quotes for Text

**Wrong:**
```sql
WHERE name = Priya
```
**Error:** Column 'Priya' doesn't exist!

**Correct:**
```sql
WHERE name = 'Priya'
```

---

### Mistake 2: Wrong Column Name

**Wrong:**
```sql
WHERE score > 80
-- But the column is actually called 'marks'
```
**Error:** Unknown column 'score'

**Correct:**
```sql
WHERE marks > 80
```

---

### Mistake 3: Case Sensitivity Issues

**Note:** Text values are case-sensitive in some databases!

```sql
WHERE country = 'germany'  -- might not match 'Germany'
WHERE country = 'Germany'  -- correct match
```

---

## Complete Example: Filtering Rows AND Columns

**Task:** Show the name and score of customers who scored more than 80.

```sql
SELECT first_name, score
FROM customers
WHERE score > 80;
```

**Sample Data:**
```
customer_id | first_name | country | score
------------|-----------|---------|-------
1           | Ravi      | India   | 75
2           | Priya     | USA     | 92
3           | Amit      | UK      | 85
4           | Sneha     | Germany | 78
5           | Karan     | France  | 90
```

**Expected Output:**
```
first_name | score
-----------|-------
Priya      | 92
Amit       | 85
Karan      | 90
```

**What happened?**
1. WHERE filtered out rows where score ≤ 80 (removed Ravi and Sneha)
2. SELECT chose only 2 columns to display (first_name and score)

---

## Quick Reference Chart 📊

| Want to Filter | Operator | Example |
|----------------|----------|---------|
| Exact match | `=` | `WHERE age = 13` |
| Not match | `!=` or `<>` | `WHERE age != 13` |
| Greater | `>` | `WHERE marks > 75` |
| Less | `<` | `WHERE price < 100` |
| At least | `>=` | `WHERE marks >= 80` |
| At most | `<=` | `WHERE price <= 500` |

---

## Key Takeaways 🌟

1. **WHERE filters rows** based on conditions
2. **Write WHERE after FROM** clause
3. **Use single quotes for text** values: `'India'`
4. **No quotes for numbers**: `85`
5. **Data that doesn't meet condition gets removed**
6. **You can filter rows AND columns together**
7. **Operators: =, !=, >, <, >=, <=**

---

## What's Next? 🚀

You've learned:
- ✅ SELECT (choose columns)
- ✅ FROM (choose table)
- ✅ WHERE (filter rows)

**Next topic:** ORDER BY - to sort your results in ascending or descending order! 📈📉

Now you can control WHAT to show (SELECT), WHERE to get it (FROM), and WHICH rows to keep (WHERE)! 🎯
