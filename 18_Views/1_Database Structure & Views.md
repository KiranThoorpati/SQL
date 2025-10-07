# Database Structure & Introduction to Views
*Understanding How Databases Are Organized*

## The Big Picture: Database Hierarchy

Think of a database like your school building:
- **SQL Server** = The entire school campus
- **Database** = Different buildings (Main Building, Sports Complex, Library)
- **Schema** = Floors in each building (Ground Floor, First Floor)
- **Tables & Views** = Rooms on each floor (Classrooms, Labs)
- **Columns** = Desks and chairs inside rooms

---

## The Complete Hierarchy

```
SQL Server (The Big Boss)
    ↓
Databases (Different projects/topics)
    ↓
Schemas (Logical groups)
    ↓
Tables & Views (Data containers)
    ↓
Columns & Rows (Actual data)
```

---

## Level-by-Level Explanation

### Level 1: SQL Server 🖥️
**What is it?** The control center that manages everything

**Example:** Your computer running SQL Server

**Think of it as:** The principal who manages the entire school

---

### Level 2: Databases 📚
**What is it?** A collection of related information stored in an organized way

**Example:** 
- `SchoolDB` - All school data
- `LibraryDB` - All library data
- `SportsDB` - All sports data

**Think of it as:** Different departments in your school

**SQL Example:**
```sql
-- Create a new database
CREATE DATABASE SchoolDB;

-- Use the database
USE SchoolDB;
```

---

### Level 3: Schemas 📁
**What is it?** A logical way to group related objects (tables, views) together

**Example in a School Database:**
- `Students` schema - All student-related tables
- `Teachers` schema - All teacher-related tables
- `Courses` schema - All course-related tables

**Think of it as:** Different sections or wings in a building

**SQL Example:**
```sql
-- Create schemas
CREATE SCHEMA Students;
CREATE SCHEMA Teachers;
CREATE SCHEMA Courses;
```

---

### Level 4A: Tables 📊
**What is it?** Where data is actually stored (physical storage)

**Key Points:**
- ✅ **Stores data permanently**
- ✅ Has rows and columns
- ✅ Data exists on disk

**Example:**
```sql
-- Create a table in Students schema
CREATE TABLE Students.StudentInfo (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    class VARCHAR(10),
    age INT
);

-- Insert data (stored permanently)
INSERT INTO Students.StudentInfo VALUES
(1, 'Alice', '7A', 13),
(2, 'Bob', '7B', 12),
(3, 'Charlie', '7A', 13);

-- View the data
SELECT * FROM Students.StudentInfo;
```

**Output:**
```
student_id | name    | class | age
-----------|---------|-------|----
1          | Alice   | 7A    | 13
2          | Bob     | 7B    | 12
3          | Charlie | 7A    | 13
```

---

### Level 4B: Views 👁️
**What is it?** A virtual table that shows data but doesn't store it

**Key Points:**
- ❌ **Does NOT store data**
- ✅ Acts like a table but is just a saved query
- ✅ Shows fresh data every time you query it

**Think of it as:** A window that shows you a view of the actual room (table), but the window itself doesn't contain anything

---

## Table vs View: The Big Difference

| Feature | Table | View |
|---------|-------|------|
| **Stores Data** | ✅ Yes (permanently) | ❌ No (virtual) |
| **Takes Space** | ✅ Yes (on disk) | ❌ Minimal (just query) |
| **Updates Automatically** | ❌ No (manual insert) | ✅ Yes (reflects table changes) |
| **Physical/Virtual** | Physical | Virtual |
| **Think of it as** | A file cabinet | A window to the cabinet |

---

## Complete Example: School Database

Let's build a complete database structure step by step!

### Step 1: Create Database
```sql
CREATE DATABASE SchoolManagement;
USE SchoolManagement;
```

### Step 2: Create Schemas
```sql
CREATE SCHEMA Academic;
CREATE SCHEMA Administration;
```

### Step 3: Create Tables
```sql
-- Table 1: Students (in Academic schema)
CREATE TABLE Academic.Students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    class VARCHAR(10),
    section CHAR(1)
);

-- Table 2: Grades (in Academic schema)
CREATE TABLE Academic.Grades (
    grade_id INT PRIMARY KEY,
    student_id INT,
    subject VARCHAR(30),
    marks INT
);

-- Insert sample data
INSERT INTO Academic.Students VALUES
(1, 'Alice', '7th', 'A'),
(2, 'Bob', '7th', 'B'),
(3, 'Charlie', '8th', 'A'),
(4, 'Diana', '8th', 'B');

INSERT INTO Academic.Grades VALUES
(1, 1, 'Math', 85),
(2, 1, 'Science', 92),
(3, 2, 'Math', 78),
(4, 2, 'Science', 88),
(5, 3, 'Math', 95),
(6, 3, 'Science', 90);
```

---

### Step 4: Create a View

Now let's create a VIEW that shows student names with their average marks:

```sql
-- Create a view (doesn't store data, just a saved query)
CREATE VIEW Academic.StudentAverages AS
SELECT 
    s.student_id,
    s.name,
    s.class,
    AVG(g.marks) as average_marks
FROM Academic.Students s
JOIN Academic.Grades g ON s.student_id = g.student_id
GROUP BY s.student_id, s.name, s.class;
```

**Now use the view like a table:**
```sql
-- Query the view
SELECT * FROM Academic.StudentAverages;
```

**Output:**
```
student_id | name    | class | average_marks
-----------|---------|-------|---------------
1          | Alice   | 7th   | 88.50
2          | Bob     | 7th   | 83.00
3          | Charlie | 8th   | 92.50
```

**Important:** This data is NOT stored in the view! The view runs the query every time you use it.

---

## Why Views Are Like Magic Windows 🪟

### Example: What Happens When Table Data Changes?

```sql
-- Add new grade for Alice
INSERT INTO Academic.Grades VALUES
(7, 1, 'English', 95);

-- Query the view again (no changes needed!)
SELECT * FROM Academic.StudentAverages;
```

**New Output:**
```
student_id | name    | class | average_marks
-----------|---------|-------|---------------
1          | Alice   | 7th   | 90.67  ← Changed automatically!
2          | Bob     | 7th   | 83.00
3          | Charlie | 8th   | 92.50
```

**The view automatically shows updated data because it re-runs the query each time!**

---

## DDL Commands: Managing Database Structure

**DDL = Data Definition Language** (Commands to create/modify structure)

### 1. CREATE - Make New Objects
```sql
-- Create database
CREATE DATABASE MySchool;

-- Create schema
CREATE SCHEMA Students;

-- Create table
CREATE TABLE Students.Info (
    id INT,
    name VARCHAR(50)
);

-- Create view
CREATE VIEW Students.Summary AS
SELECT * FROM Students.Info;
```

---

### 2. ALTER - Modify Existing Objects
```sql
-- Add a new column to table
ALTER TABLE Students.Info
ADD age INT;

-- Modify column type
ALTER TABLE Students.Info
ALTER COLUMN name VARCHAR(100);
```

---

### 3. DROP - Delete Objects
```sql
-- Drop a view
DROP VIEW Students.Summary;

-- Drop a table
DROP TABLE Students.Info;

-- Drop a schema
DROP SCHEMA Students;

-- Drop a database
DROP DATABASE MySchool;
```

**⚠️ Warning:** DROP permanently deletes! Be careful!

---

## Viewing Database Structure in SQL Server

### See All Databases:
```sql
SELECT name FROM sys.databases;
```

### See All Schemas in Current Database:
```sql
SELECT name FROM sys.schemas
WHERE name NOT IN ('sys', 'INFORMATION_SCHEMA', 'guest');
```

### See All Tables in a Schema:
```sql
SELECT TABLE_NAME 
FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_SCHEMA = 'Academic'
AND TABLE_TYPE = 'BASE TABLE';
```

### See All Views in a Schema:
```sql
SELECT TABLE_NAME 
FROM INFORMATION_SCHEMA.VIEWS 
WHERE TABLE_SCHEMA = 'Academic';
```

### See All Columns in a Table:
```sql
SELECT COLUMN_NAME, DATA_TYPE 
FROM INFORMATION_SCHEMA.COLUMNS 
WHERE TABLE_NAME = 'Students';
```

---

## Real-World Naming Convention

When you see: `Sales.Customers`

**Breaking it down:**
- `Sales` = Schema name (the group)
- `Customers` = Table/View name (the object)

**Full path:** `DatabaseName.SchemaName.ObjectName`

Example: `SchoolDB.Academic.Students`

---

## Practice Example: Library System

```sql
-- 1. Create database
CREATE DATABASE LibraryDB;
USE LibraryDB;

-- 2. Create schema
CREATE SCHEMA Library;

-- 3. Create tables
CREATE TABLE Library.Books (
    book_id INT PRIMARY KEY,
    title VARCHAR(100),
    author VARCHAR(50),
    available INT
);

CREATE TABLE Library.Borrowed (
    borrow_id INT PRIMARY KEY,
    book_id INT,
    student_name VARCHAR(50),
    borrow_date DATE
);

-- 4. Insert data
INSERT INTO Library.Books VALUES
(1, 'Harry Potter', 'J.K. Rowling', 5),
(2, 'Python Guide', 'John Smith', 3),
(3, 'Math Fun', 'Sarah Lee', 8);

INSERT INTO Library.Borrowed VALUES
(1, 1, 'Alice', '2024-01-10'),
(2, 1, 'Bob', '2024-01-15'),
(3, 2, 'Charlie', '2024-01-12');

-- 5. Create a view showing available books
CREATE VIEW Library.AvailableBooks AS
SELECT 
    b.book_id,
    b.title,
    b.author,
    b.available,
    COUNT(br.borrow_id) as times_borrowed
FROM Library.Books b
LEFT JOIN Library.Borrowed br ON b.book_id = br.book_id
GROUP BY b.book_id, b.title, b.author, b.available;

-- 6. Use the view
SELECT * FROM Library.AvailableBooks;
```

**Expected Output:**
```
book_id | title         | author        | available | times_borrowed
--------|---------------|---------------|-----------|----------------
1       | Harry Potter  | J.K. Rowling  | 5         | 2
2       | Python Guide  | John Smith    | 3         | 1
3       | Math Fun      | Sarah Lee     | 8         | 0
```

---

## Key Takeaways

📌 **Hierarchy:** SQL Server → Database → Schema → Tables/Views → Columns  
📌 **Tables** = Store data permanently (physical)  
📌 **Views** = Virtual tables, don't store data (just saved queries)  
📌 **Schemas** = Logical grouping of related objects  
📌 **DDL Commands** = CREATE, ALTER, DROP  
📌 **Views auto-update** = Always show current table data  
📌 **Naming format:** `Schema.ObjectName` (e.g., `Sales.Customers`)  

**Next up:** We'll learn how to create and use views in detail! 🎯
