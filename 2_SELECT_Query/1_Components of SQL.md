# SQL Components - A Beginner's Guide

## What is an SQL Query?

An SQL query is a set of instructions we write to communicate with a database. Think of it like writing a letter to a librarian asking for specific books - you need to be clear about what you want!

When we write SQL code that starts with SELECT, we call it an **SQL SELECT statement** or **SQL query**.

---

## The Building Blocks of SQL

Let's look at the main parts that make up any SQL code:

### 1. **Comments** 💬
Comments are notes you write for yourself or other programmers. The computer ignores them completely!

**Example:**
```sql
-- This is a comment. SQL won't read this line!
/* This is also a comment 
   that can span multiple lines */
```

---

### 2. **Clauses** 📋
Clauses are the main sections of your SQL query. Each clause has a specific job:

- **SELECT clause**: Chooses which columns you want to see
- **FROM clause**: Tells which table to get data from
- **WHERE clause**: Filters the data (like using a strainer)

**Example:**
```sql
SELECT name, age          -- SELECT clause
FROM students             -- FROM clause
WHERE age > 12;           -- WHERE clause
```

**Expected Output:**
```
name       | age
-----------|-----
Rahul      | 13
Priya      | 14
Amit       | 15
```

---

### 3. **Keywords** 🔑
Keywords are special words that SQL understands. They're always shown in **BLUE** and written in **UPPERCASE** (capital letters).

Common keywords: SELECT, FROM, WHERE, AND, OR, ORDER BY

**Best Practice:** Always write keywords in UPPERCASE to make your code easier to read!

**Example:**
```sql
SELECT * FROM books;  -- Good! Keywords in uppercase
```

---

### 4. **Functions** ⚙️
Functions are like mini-machines that take input, process it, and give output. They transform your data!

Common functions:
- **LOWER()** - converts text to lowercase
- **UPPER()** - converts text to UPPERCASE
- **COUNT()** - counts how many rows there are

**Example:**
```sql
SELECT name, LOWER(city) AS lowercase_city
FROM students;
```

**Input Data (students table):**
```
name    | city
--------|----------
Ravi    | DELHI
Sneha   | MUMBAI
```

**Expected Output:**
```
name    | lowercase_city
--------|---------------
Ravi    | delhi
Sneha   | mumbai
```

---

### 5. **Identifiers** 🏷️
Identifiers are simply names - names of tables, columns, or databases. They appear in **BLACK** text.

**Example:**
```sql
SELECT student_name, marks    -- student_name and marks are identifiers (column names)
FROM exam_results;            -- exam_results is an identifier (table name)
```

---

### 6. **Operators** ⚖️
Operators help us compare values. They're mostly used in the WHERE clause.

Common operators:
- **=** (equal to)
- **>** (greater than)
- **<** (less than)
- **>=** (greater than or equal to)
- **<=** (less than or equal to)
- **!=** or **<>** (not equal to)

**Example:**
```sql
SELECT name, marks
FROM students
WHERE marks >= 75;
```

**Input Data:**
```
name     | marks
---------|-------
Arjun    | 85
Meera    | 70
Karan    | 90
Sita     | 65
```

**Expected Output:**
```
name     | marks
---------|-------
Arjun    | 85
Karan    | 90
```

---

### 7. **Static Values** 📌
Static values are fixed values you type directly into your query. They appear in **RED** text and are usually surrounded by single quotes (' ').

**Example:**
```sql
SELECT name, city
FROM students
WHERE city = 'Mumbai';    -- 'Mumbai' is a static value
```

**Expected Output:**
```
name     | city
---------|--------
Priya    | Mumbai
Rohan    | Mumbai
```

---

## Complete Example with All Components

Let's see everything together:

```sql
-- This query finds students from Delhi who scored above 80
SELECT name, UPPER(city) AS city_uppercase, marks
FROM students
WHERE city = 'Delhi' AND marks > 80;
```

**Input Data (students table):**
```
name     | city      | marks
---------|-----------|-------
Rahul    | Delhi     | 85
Priya    | Mumbai    | 90
Amit     | Delhi     | 75
Sneha    | Delhi     | 88
Karan    | Bangalore | 82
```

**Expected Output:**
```
name     | city_uppercase | marks
---------|----------------|-------
Rahul    | DELHI          | 85
Sneha    | DELHI          | 88
```

**Breaking it down:**
- **Comment**: The green line starting with --
- **Keywords**: SELECT, FROM, WHERE, AND (in blue, uppercase)
- **Function**: UPPER() transforms city names
- **Identifiers**: name, city, marks, students (column and table names)
- **Operators**: = and > (comparing values)
- **Static values**: 'Delhi' and 80 (fixed values we're searching for)

---

## Remember! 🌟

These components appear in **ALL** types of SQL statements - not just SELECT, but also INSERT, UPDATE, DELETE, and CREATE. Once you understand these basics, you can read and write any SQL code!
