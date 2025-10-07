# SQL Tables: A Complete Introduction

## Understanding Database Structure

Think of a SQL Server like a huge library building:
- **SQL Server** = The entire library building
- **Databases** = Different floors (Fiction floor, Science floor, etc.)
- **Schemas** = Different sections on each floor (Children's section, Adult section, etc.)
- **Objects** = Individual items like bookshelves, tables, and reading desks

Inside each schema, we can create different objects:
- **Tables** = Where actual data is stored (like bookshelves holding books)
- **Views** = Virtual windows showing selected data (like book catalogs)

---

## What is DDL (Data Definition Language)?

DDL is a set of SQL commands used to create and manage database structure:

| Command | Purpose | Example |
|---------|---------|---------|
| **CREATE** | Make a new table | `CREATE TABLE students` |
| **ALTER** | Modify existing table | `ALTER TABLE students ADD age INT` |
| **DROP** | Delete a table completely | `DROP TABLE students` |

---

## Three Levels of Database Architecture

```
┌─────────────────────────────────────┐
│  HIGH LEVEL (External Level)        │
│  • End Users, Business Analysts     │
│  • Work with: Reports, Dashboards   │
│  • Easiest to understand            │
├─────────────────────────────────────┤
│  MIDDLE LEVEL (Conceptual/Logical)  │ ← WE WORK HERE!
│  • Developers, Data Engineers       │
│  • Work with: Tables, Relationships │
│  • Medium difficulty                │
├─────────────────────────────────────┤
│  LOW LEVEL (Physical Level)         │
│  • Database Administrators          │
│  • Work with: Disk storage, files   │
│  • Most complex                     │
└─────────────────────────────────────┘
```

As students/developers, we work at the **middle level** with tables - not too easy, not too hard!

---

## What is a Database Table?

A **table** is like a spreadsheet in Excel with:
- **Columns** = Fields (like headers: ID, Name, Grade)
- **Rows** = Records (each row is one entry)
- **Cells** = Individual pieces of data (intersection of row and column)

### Visual Example:

```
STUDENTS TABLE
┌────┬─────────────┬────────┬───────┐
│ ID │    Name     │ Class  │ Marks │  ← Column names (fields)
├────┼─────────────┼────────┼───────┤
│ 1  │ Aarav Kumar │   7A   │  85   │  ← Row 1 (one student record)
│ 2  │ Diya Patel  │   7B   │  92   │  ← Row 2 (another student)
│ 3  │ Rohan Singh │   7A   │  78   │  ← Row 3
└────┴─────────────┴────────┴───────┘
     ↑                      ↑
   Cell              Cell (Diya's marks)
```

---

## How Tables are Actually Stored

**What you see:** A nice, organized table like a spreadsheet

**What actually happens:** 
- Data is stored in special **database files** on the computer's hard disk
- These files are NOT like Excel files
- Regular users can't directly access these files
- When you query a table, the database fetches data from these files and shows it to you in table format

Think of it like:
- You order food at a restaurant (you query the table)
- The kitchen prepares it from ingredients stored in the refrigerator (database files)
- You get a nice plate of food (the table result you see)

---

## Types of Tables

### 1. **Permanent Tables**
- Stay in the database forever (until you manually delete them)
- Like files saved on your computer

### 2. **Temporary Tables**
- Automatically deleted when you close the session
- Like items in a shopping cart that disappear when you close the browser

---

## Two Ways to Create Permanent Tables

### Method 1: CREATE + INSERT (From Scratch)
1. First, create an empty table structure
2. Then, insert data row by row

### Method 2: CREATE TABLE AS SELECT (From Query)
- Create a table and fill it with data from a query in one step

Let's see both methods with examples!

---

## Method 1: CREATE + INSERT

### Step 1: Create Empty Table

```sql
-- Define table structure
CREATE TABLE students (
    student_id INT,
    student_name VARCHAR(50),
    class VARCHAR(10),
    marks INT,
    attendance_percent INT
);
```

**What this does:**
- Creates a table named `students`
- Defines 5 columns with their data types
- Table is empty (no data yet)

### Step 2: Check Empty Table

```sql
SELECT * FROM students;
```

**Output:**
```
(0 rows)
```

The table exists but has no data!

### Step 3: Insert Data

```sql
-- Add first student
INSERT INTO students VALUES (1, 'Aarav Kumar', '7A', 85, 95);

-- Add second student
INSERT INTO students VALUES (2, 'Diya Patel', '7B', 92, 98);

-- Add third student
INSERT INTO students VALUES (3, 'Rohan Singh', '7A', 78, 88);

-- Add multiple students at once
INSERT INTO students VALUES 
(4, 'Priya Sharma', '7C', 88, 92),
(5, 'Arjun Reddy', '7B', 95, 96);
```

### Step 4: View the Data

```sql
SELECT * FROM students;
```

**Output:**
```
student_id | student_name  | class | marks | attendance_percent
-----------|---------------|-------|-------|-------------------
1          | Aarav Kumar   | 7A    | 85    | 95
2          | Diya Patel    | 7B    | 92    | 98
3          | Rohan Singh   | 7A    | 78    | 88
4          | Priya Sharma  | 7C    | 88    | 92
5          | Arjun Reddy   | 7B    | 95    | 96
```

---

## Method 2: CREATE TABLE AS SELECT

This method creates a table and fills it with data in **one step**!

### Example: Create a Table from Query Results

```sql
-- First, let's assume we have an existing table with test scores
CREATE TABLE exam_results (
    student_id INT,
    student_name VARCHAR(50),
    math_score INT,
    science_score INT,
    english_score INT
);

-- Add some data
INSERT INTO exam_results VALUES
(1, 'Aarav Kumar', 85, 90, 82),
(2, 'Diya Patel', 92, 88, 95),
(3, 'Rohan Singh', 78, 82, 80),
(4, 'Priya Sharma', 88, 92, 86),
(5, 'Arjun Reddy', 95, 93, 97);
```

**Now create a new table with calculated data:**

```sql
-- Create table with total scores and averages
CREATE TABLE student_performance AS
SELECT 
    student_id,
    student_name,
    math_score,
    science_score,
    english_score,
    (math_score + science_score + english_score) AS total_score,
    (math_score + science_score + english_score) / 3 AS average_score
FROM exam_results;
```

**What this does:**
- Creates a NEW table called `student_performance`
- Automatically fills it with data from the SELECT query
- Includes calculated columns (total_score and average_score)

### View the New Table

```sql
SELECT * FROM student_performance;
```

**Output:**
```
student_id | student_name  | math_score | science_score | english_score | total_score | average_score
-----------|---------------|------------|---------------|---------------|-------------|---------------
1          | Aarav Kumar   | 85         | 90            | 82            | 257         | 85
2          | Diya Patel    | 92         | 88            | 95            | 275         | 91
3          | Rohan Singh   | 78         | 82            | 80            | 240         | 80
4          | Priya Sharma  | 88         | 92            | 86            | 266         | 88
5          | Arjun Reddy   | 95         | 93            | 97            | 285         | 95
```

---

## Comparing Both Methods

| Feature | CREATE + INSERT | CREATE AS SELECT |
|---------|----------------|------------------|
| **Steps** | Two steps (create, then insert) | One step |
| **Speed** | Slower (manual insertion) | Faster |
| **Use When** | Building from scratch | Creating from existing data |
| **Flexibility** | More control over each row | Based on query results |
| **Best For** | Small amounts of data | Large datasets or transformations |

---

## Practical Example: School Library System

### Method 1: Create + Insert

```sql
-- Create book table
CREATE TABLE library_books (
    book_id INT,
    book_title VARCHAR(100),
    author VARCHAR(50),
    available_copies INT
);

-- Add books one by one
INSERT INTO library_books VALUES
(1, 'Harry Potter', 'J.K. Rowling', 5),
(2, 'The Jungle Book', 'Rudyard Kipling', 3),
(3, 'Matilda', 'Roald Dahl', 4);

SELECT * FROM library_books;
```

**Output:**
```
book_id | book_title       | author           | available_copies
--------|------------------|------------------|------------------
1       | Harry Potter     | J.K. Rowling     | 5
2       | The Jungle Book  | Rudyard Kipling  | 3
3       | Matilda          | Roald Dahl       | 4
```

### Method 2: Create from Query

```sql
-- Create popular books table (books with 4+ copies)
CREATE TABLE popular_books AS
SELECT 
    book_id,
    book_title,
    author,
    available_copies,
    'High Demand' AS status
FROM library_books
WHERE available_copies >= 4;

SELECT * FROM popular_books;
```

**Output:**
```
book_id | book_title   | author       | available_copies | status
--------|--------------|--------------|------------------|-------------
1       | Harry Potter | J.K. Rowling | 5                | High Demand
3       | Matilda      | Roald Dahl   | 4                | High Demand
```

---

## Summary

**Tables are:**
- 📊 Structured collections of data (like spreadsheets)
- 💾 Stored physically in database files on disk
- 🏗️ Created using DDL commands (CREATE, ALTER, DROP)
- 📝 Organized in rows (records) and columns (fields)

**Two ways to create tables:**
1. **CREATE + INSERT**: Build structure first, add data later
2. **CREATE AS SELECT**: Build and fill in one step from query

**Key Point:** Tables store actual data permanently (unlike views which are virtual), but you don't see the actual database files - you work with the table abstraction!
