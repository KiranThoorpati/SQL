# SQL: CREATE TABLE Syntax - Both Methods Explained

## Method 1: CREATE + INSERT (Classical Way)

This method has **TWO separate steps**.

---

### Step 1: CREATE TABLE (Define Structure)

**Basic Syntax:**
```sql
CREATE TABLE table_name (
    column1_name data_type,
    column2_name data_type,
    column3_name data_type
);
```

**Breaking it down:**
- `CREATE TABLE` = Command to make a new table
- `table_name` = Name you give to your table
- `( )` = Parentheses hold the column definitions
- `column_name` = Name of each column
- `data_type` = Type of data (INT, VARCHAR, etc.)

---

### Step 2: INSERT INTO (Add Data)

**Basic Syntax:**
```sql
INSERT INTO table_name VALUES (value1, value2, value3);
```

---

### Complete Example: Student Records

```sql
-- STEP 1: Create empty table structure
CREATE TABLE students (
    id INT,
    name VARCHAR(50),
    age INT,
    class VARCHAR(10)
);
```

**What we created:**
```
Table: students
Columns: id (number), name (text up to 50 chars), age (number), class (text up to 10 chars)
Data: EMPTY
```

**Check the empty table:**
```sql
SELECT * FROM students;
```

**Output:**
```
(0 rows - table is empty)
```

---

```sql
-- STEP 2: Insert data into the table
INSERT INTO students VALUES (1, 'Aarav', 13, '7A');
INSERT INTO students VALUES (2, 'Diya', 12, '7B');
INSERT INTO students VALUES (3, 'Rohan', 13, '7A');

-- Or insert multiple rows at once
INSERT INTO students VALUES 
(4, 'Priya', 12, '7C'),
(5, 'Arjun', 13, '7B');
```

**Now check the table:**
```sql
SELECT * FROM students;
```

**Output:**
```
id | name  | age | class
---|-------|-----|-------
1  | Aarav | 13  | 7A
2  | Diya  | 12  | 7B
3  | Rohan | 13  | 7A
4  | Priya | 12  | 7C
5  | Arjun | 13  | 7B
```

✅ **Table created and filled in TWO steps!**

---

## Method 2: CREATE TABLE AS SELECT (CTAS)

This method does **EVERYTHING in ONE step**.

There are **TWO different syntax styles** depending on your database:

---

### Style 1: Standard Syntax (MySQL, PostgreSQL, Oracle)

**Syntax:**
```sql
CREATE TABLE new_table_name AS (
    SELECT column1, column2, ...
    FROM existing_table
    WHERE conditions
);
```

**Breaking it down:**
- `CREATE TABLE` = Make a new table
- `new_table_name` = Name for the new table
- `AS` = Keyword meaning "based on"
- `( )` = Parentheses contain your query
- `SELECT...` = Your query that fetches data

---

### Style 2: SQL Server Syntax (SELECT INTO)

**Syntax:**
```sql
SELECT column1, column2, ...
INTO new_table_name
FROM existing_table
WHERE conditions;
```

**Breaking it down:**
- `SELECT` = Start with column selection
- `INTO new_table_name` = Special keyword creates new table
- `FROM...` = Continue with normal query

---

## Complete Example: Both Syntax Styles

### Setup: Create Source Table

```sql
-- Create and fill a source table
CREATE TABLE all_students (
    student_id INT,
    student_name VARCHAR(50),
    marks INT,
    subject VARCHAR(30)
);

INSERT INTO all_students VALUES
(1, 'Aarav', 85, 'Math'),
(2, 'Diya', 92, 'Math'),
(3, 'Rohan', 78, 'Math'),
(4, 'Priya', 95, 'Science'),
(5, 'Arjun', 88, 'Science'),
(6, 'Sneha', 72, 'Math');
```

**Check source table:**
```sql
SELECT * FROM all_students;
```

**Output:**
```
student_id | student_name | marks | subject
-----------|--------------|-------|----------
1          | Aarav        | 85    | Math
2          | Diya         | 92    | Math
3          | Rohan        | 78    | Math
4          | Priya        | 95    | Science
5          | Arjun        | 88    | Science
6          | Sneha        | 72    | Math
```

---

### Example 1: Standard CTAS Syntax

**Create table of high scorers (marks >= 85):**

```sql
CREATE TABLE high_scorers AS (
    SELECT 
        student_id,
        student_name,
        marks,
        'Excellent' AS grade
    FROM all_students
    WHERE marks >= 85
);
```

**What happened:**
1. ✅ Query executed
2. ✅ New table `high_scorers` created
3. ✅ Data automatically inserted
4. ✅ New column `grade` added with value 'Excellent'

**Check the new table:**
```sql
SELECT * FROM high_scorers;
```

**Output:**
```
student_id | student_name | marks | grade
-----------|--------------|-------|----------
1          | Aarav        | 85    | Excellent
2          | Diya         | 92    | Excellent
4          | Priya        | 95    | Excellent
5          | Arjun        | 88    | Excellent
```

---

### Example 2: SQL Server Syntax (SELECT INTO)

**Create table of math students:**

```sql
SELECT 
    student_id,
    student_name,
    marks,
    CASE 
        WHEN marks >= 90 THEN 'A'
        WHEN marks >= 80 THEN 'B'
        ELSE 'C'
    END AS letter_grade
INTO math_students
FROM all_students
WHERE subject = 'Math';
```

**Notice:** The `INTO math_students` is inserted between SELECT and FROM!

**Check the new table:**
```sql
SELECT * FROM math_students;
```

**Output:**
```
student_id | student_name | marks | letter_grade
-----------|--------------|-------|-------------
1          | Aarav        | 85    | B
2          | Diya         | 92    | A
3          | Rohan        | 78    | C
6          | Sneha        | 72    | C
```

---

## Comparing Both Syntax Styles

### Standard Syntax (MySQL, PostgreSQL, Oracle)

```sql
CREATE TABLE top_performers AS (
    SELECT student_name, marks
    FROM all_students
    WHERE marks >= 90
);
```

**Advantages:**
- ✅ Clear separation: DDL (CREATE) separate from query
- ✅ Easy to read and understand
- ✅ DDL command is visible at the top

---

### SQL Server Syntax (SELECT INTO)

```sql
SELECT student_name, marks
INTO top_performers
FROM all_students
WHERE marks >= 90;
```

**Advantages:**
- ✅ Shorter syntax
- ✅ Looks like a normal SELECT query

**Disadvantages:**
- ⚠️ `INTO` can be easy to miss in long queries
- ⚠️ DDL hidden inside the query

---

## Side-by-Side Comparison

| Feature | Standard Syntax | SQL Server Syntax |
|---------|----------------|-------------------|
| **Command** | `CREATE TABLE...AS` | `SELECT...INTO` |
| **Databases** | MySQL, PostgreSQL, Oracle | SQL Server |
| **Readability** | Very clear | Can be confusing |
| **Query visibility** | Outside the query | Inside the query |

---

## Complex Query Example

### Standard Syntax (Recommended)

```sql
CREATE TABLE student_summary AS (
    SELECT 
        student_name,
        subject,
        marks,
        marks * 1.1 AS bonus_marks,
        CASE 
            WHEN marks >= 90 THEN 'Outstanding'
            WHEN marks >= 80 THEN 'Excellent'
            WHEN marks >= 70 THEN 'Good'
            ELSE 'Needs Improvement'
        END AS performance
    FROM all_students
    WHERE marks > 70
    ORDER BY marks DESC
);
```

**Easy to see:** `CREATE TABLE` is clearly visible at the top!

**Check result:**
```sql
SELECT * FROM student_summary;
```

**Output:**
```
student_name | subject | marks | bonus_marks | performance
-------------|---------|-------|-------------|-------------
Priya        | Science | 95    | 104.5       | Outstanding
Diya         | Math    | 92    | 101.2       | Outstanding
Arjun        | Science | 88    | 96.8        | Excellent
Aarav        | Math    | 85    | 93.5        | Excellent
Rohan        | Math    | 78    | 85.8        | Good
Sneha        | Math    | 72    | 79.2        | Good
```

---

### SQL Server Syntax (INTO in the middle)

```sql
SELECT 
    student_name,
    subject,
    marks,
    marks * 1.1 AS bonus_marks,
    CASE 
        WHEN marks >= 90 THEN 'Outstanding'
        WHEN marks >= 80 THEN 'Excellent'
        WHEN marks >= 70 THEN 'Good'
        ELSE 'Needs Improvement'
    END AS performance
INTO student_summary            -- ⚠️ Easy to miss in long query!
FROM all_students
WHERE marks > 70
ORDER BY marks DESC;
```

**Problem:** In a long query, the `INTO` can be hard to spot!

---

## Real-World Example: School Library System

### Scenario: Create a table of overdue books

**Setup:**
```sql
CREATE TABLE book_issues (
    issue_id INT,
    student_name VARCHAR(50),
    book_title VARCHAR(100),
    issue_date DATE,
    days_overdue INT
);

INSERT INTO book_issues VALUES
(1, 'Aarav', 'Harry Potter', '2025-09-01', 15),
(2, 'Diya', 'Matilda', '2025-09-20', 5),
(3, 'Rohan', 'Jungle Book', '2025-08-25', 25),
(4, 'Priya', 'Charlotte Web', '2025-09-28', 2);
```

---

### Using Standard CTAS Syntax

```sql
CREATE TABLE overdue_books AS (
    SELECT 
        student_name,
        book_title,
        days_overdue,
        days_overdue * 2 AS fine_amount,
        CASE 
            WHEN days_overdue > 20 THEN 'High Priority'
            WHEN days_overdue > 10 THEN 'Medium Priority'
            ELSE 'Low Priority'
        END AS priority
    FROM book_issues
    WHERE days_overdue > 7
);
```

**Check result:**
```sql
SELECT * FROM overdue_books;
```

**Output:**
```
student_name | book_title  | days_overdue | fine_amount | priority
-------------|-------------|--------------|-------------|----------------
Aarav        | Harry Potter| 15           | 30          | Medium Priority
Rohan        | Jungle Book | 25           | 50          | High Priority
```

---

## Quick Reference Guide

### Method 1: CREATE + INSERT
```sql
-- Step 1: Create structure
CREATE TABLE table_name (
    column1 data_type,
    column2 data_type
);

-- Step 2: Insert data
INSERT INTO table_name VALUES (value1, value2);
```

---

### Method 2A: Standard CTAS
```sql
CREATE TABLE new_table AS (
    SELECT columns
    FROM source_table
    WHERE conditions
);
```

---

### Method 2B: SQL Server CTAS
```sql
SELECT columns
INTO new_table
FROM source_table
WHERE conditions;
```

---

## Which Syntax Should You Use?

### Use Standard Syntax (CREATE TABLE AS) when:
- ✅ You want clear, readable code
- ✅ Working with MySQL, PostgreSQL, Oracle
- ✅ Writing complex queries
- ✅ You want DDL clearly visible

### Use SQL Server Syntax (SELECT INTO) when:
- ✅ Working specifically with SQL Server
- ✅ Writing simple, short queries
- ✅ You prefer compact syntax

---

## Common Data Types Reference

| Data Type | Used For | Example |
|-----------|----------|---------|
| `INT` | Whole numbers | 1, 42, 100 |
| `VARCHAR(n)` | Text (up to n characters) | 'Aarav', 'Hello' |
| `DATE` | Dates | '2025-01-15' |
| `DECIMAL(10,2)` | Numbers with decimals | 99.99, 1234.56 |
| `BOOLEAN` | True/False | TRUE, FALSE |

---

## Summary

**CREATE + INSERT (Classical):**
- Two separate steps
- More control over structure
- Used when starting from scratch

**CREATE TABLE AS SELECT (CTAS):**
- One step (query creates table)
- Two syntax styles (standard vs SQL Server)
- Used when creating from existing data

**Best Practice:** Use **standard CTAS syntax** (CREATE TABLE AS) because the DDL command is clearly visible and separated from the query logic!
