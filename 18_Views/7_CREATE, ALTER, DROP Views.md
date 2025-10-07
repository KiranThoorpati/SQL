# Creating, Altering, and Dropping Views
*Complete Guide to VIEW Commands*

## View Syntax (Simple!)

```sql
CREATE VIEW ViewName AS
SELECT columns
FROM table
WHERE conditions;
```

**That's it!** Just like a CTE but with `CREATE VIEW` at the start!

---

## Complete Working Example

### Setup: Create Tables

```sql
-- Create database
CREATE DATABASE SalesDB;
USE SalesDB;

-- Create schema
CREATE SCHEMA Sales;

-- Create table
CREATE TABLE Sales.Orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    sales DECIMAL(10,2),
    quantity INT
);

-- Insert sample data
INSERT INTO Sales.Orders VALUES
(1, '2024-01-15', 1500.00, 10),
(2, '2024-01-20', 2000.00, 15),
(3, '2024-02-10', 1800.00, 12),
(4, '2024-02-25', 2200.00, 18),
(5, '2024-03-05', 1600.00, 11),
(6, '2024-03-20', 2400.00, 20);
```

---

## Step 1: Start with a Query (Using CTE)

```sql
-- First, solve using CTE
WITH MonthlySummary AS (
    SELECT 
        FORMAT(order_date, 'yyyy-MM') as order_month,
        SUM(sales) as total_sales,
        COUNT(order_id) as total_orders,
        SUM(quantity) as total_quantity
    FROM Sales.Orders
    GROUP BY FORMAT(order_date, 'yyyy-MM')
)
SELECT 
    order_month,
    total_sales,
    total_orders,
    total_quantity,
    SUM(total_sales) OVER (ORDER BY order_month) as running_total
FROM MonthlySummary
ORDER BY order_month;
```

**Output:**
```
order_month | total_sales | total_orders | total_quantity | running_total
------------|-------------|--------------|----------------|---------------
2024-01     | 3500.00     | 2            | 25             | 3500.00
2024-02     | 4000.00     | 2            | 30             | 7500.00
2024-03     | 4000.00     | 2            | 31             | 11500.00
```

**This works, but it's temporary!** Let's make it permanent with a VIEW.

---

## Step 2: CREATE VIEW - Make It Permanent!

### Basic CREATE (Default Schema):

```sql
-- Create view without specifying schema (goes to dbo)
CREATE VIEW V_MonthlySummary AS
SELECT 
    FORMAT(order_date, 'yyyy-MM') as order_month,
    SUM(sales) as total_sales,
    COUNT(order_id) as total_orders,
    SUM(quantity) as total_quantity
FROM Sales.Orders
GROUP BY FORMAT(order_date, 'yyyy-MM');
```

**Result:** View created in `dbo.V_MonthlySummary`

---

### CREATE with Schema (Recommended):

```sql
-- Create view with specific schema
CREATE VIEW Sales.V_MonthlySummary AS
SELECT 
    FORMAT(order_date, 'yyyy-MM') as order_month,
    SUM(sales) as total_sales,
    COUNT(order_id) as total_orders,
    SUM(quantity) as total_quantity
FROM Sales.Orders
GROUP BY FORMAT(order_date, 'yyyy-MM');
```

**Result:** View created in `Sales.V_MonthlySummary` ✅

---

### Use the View:

```sql
-- Query the view like a table!
SELECT * FROM Sales.V_MonthlySummary;
```

**Output:**
```
order_month | total_sales | total_orders | total_quantity
------------|-------------|--------------|----------------
2024-01     | 3500.00     | 2            | 25
2024-02     | 4000.00     | 2            | 30
2024-03     | 4000.00     | 2            | 31
```

---

### Use View in Calculations:

```sql
-- Now easily calculate running totals!
SELECT 
    order_month,
    total_sales,
    SUM(total_sales) OVER (ORDER BY order_month) as running_total
FROM Sales.V_MonthlySummary;
```

**Output:**
```
order_month | total_sales | running_total
------------|-------------|---------------
2024-01     | 3500.00     | 3500.00
2024-02     | 4000.00     | 7500.00
2024-03     | 4000.00     | 11500.00
```

**Much simpler than the CTE version!**

---

## Step 3: ALTER VIEW - Update the Logic

### Method 1: DROP then CREATE (Two Steps)

```sql
-- Step A: Drop the old view
DROP VIEW Sales.V_MonthlySummary;

-- Step B: Create new view with updated logic
CREATE VIEW Sales.V_MonthlySummary AS
SELECT 
    FORMAT(order_date, 'yyyy-MM') as order_month,
    SUM(sales) as total_sales,
    COUNT(order_id) as total_orders,
    SUM(quantity) as total_quantity,
    AVG(sales) as average_sales  -- NEW COLUMN!
FROM Sales.Orders
GROUP BY FORMAT(order_date, 'yyyy-MM');
```

---

### Method 2: ALTER VIEW (One Step)

```sql
-- Use ALTER to update existing view
ALTER VIEW Sales.V_MonthlySummary AS
SELECT 
    FORMAT(order_date, 'yyyy-MM') as order_month,
    SUM(sales) as total_sales,
    COUNT(order_id) as total_orders,
    SUM(quantity) as total_quantity,
    AVG(sales) as average_sales,      -- NEW!
    MAX(sales) as highest_sale        -- NEW!
FROM Sales.Orders
GROUP BY FORMAT(order_date, 'yyyy-MM');
```

**Query the updated view:**
```sql
SELECT * FROM Sales.V_MonthlySummary;
```

**Output (with new columns):**
```
order_month | total_sales | total_orders | total_quantity | average_sales | highest_sale
------------|-------------|--------------|----------------|---------------|-------------
2024-01     | 3500.00     | 2            | 25             | 1750.00       | 2000.00
2024-02     | 4000.00     | 2            | 30             | 2000.00       | 2200.00
2024-03     | 4000.00     | 2            | 31             | 2000.00       | 2400.00
```

---

### Method 3: CREATE OR REPLACE (SQL Server Style)

```sql
-- Check if view exists, drop it, then create
IF OBJECT_ID('Sales.V_MonthlySummary', 'V') IS NOT NULL
    DROP VIEW Sales.V_MonthlySummary;
GO

CREATE VIEW Sales.V_MonthlySummary AS
SELECT 
    FORMAT(order_date, 'yyyy-MM') as order_month,
    SUM(sales) as total_sales,
    COUNT(order_id) as total_orders,
    SUM(quantity) as total_quantity
FROM Sales.Orders
GROUP BY FORMAT(order_date, 'yyyy-MM');
GO
```

**This is the safest method - it works whether view exists or not!**

---

## Step 4: DROP VIEW - Delete It

```sql
-- Simple drop
DROP VIEW Sales.V_MonthlySummary;
```

**Check in Object Explorer → Refresh → View is gone!**

---

## Complete Example: Student Report System

### Create Tables:

```sql
CREATE TABLE Students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    class VARCHAR(10)
);

CREATE TABLE Grades (
    grade_id INT PRIMARY KEY,
    student_id INT,
    subject VARCHAR(30),
    marks INT
);

INSERT INTO Students VALUES
(1, 'Alice', '7A'),
(2, 'Bob', '7B'),
(3, 'Charlie', '7A');

INSERT INTO Grades VALUES
(1, 1, 'Math', 85),
(2, 1, 'Science', 92),
(3, 2, 'Math', 78),
(4, 2, 'Science', 88),
(5, 3, 'Math', 95),
(6, 3, 'Science', 90);
```

---

### CREATE View:

```sql
-- Create a student performance view
CREATE VIEW StudentPerformance AS
SELECT 
    s.student_id,
    s.name,
    s.class,
    AVG(g.marks) as average_marks,
    COUNT(g.grade_id) as subjects_taken
FROM Students s
JOIN Grades g ON s.student_id = g.student_id
GROUP BY s.student_id, s.name, s.class;
```

**Use it:**
```sql
SELECT * FROM StudentPerformance;
```

**Output:**
```
student_id | name    | class | average_marks | subjects_taken
-----------|---------|-------|---------------|----------------
1          | Alice   | 7A    | 88.50         | 2
2          | Bob     | 7B    | 83.00         | 2
3          | Charlie | 7A    | 92.50         | 2
```

---

### ALTER View (Add Ranking):

```sql
ALTER VIEW StudentPerformance AS
SELECT 
    s.student_id,
    s.name,
    s.class,
    AVG(g.marks) as average_marks,
    COUNT(g.grade_id) as subjects_taken,
    MAX(g.marks) as highest_mark,
    MIN(g.marks) as lowest_mark
FROM Students s
JOIN Grades g ON s.student_id = g.student_id
GROUP BY s.student_id, s.name, s.class;
```

**Use updated view:**
```sql
SELECT * FROM StudentPerformance
ORDER BY average_marks DESC;
```

**Output:**
```
student_id | name    | class | average_marks | subjects_taken | highest_mark | lowest_mark
-----------|---------|-------|---------------|----------------|--------------|------------
3          | Charlie | 7A    | 92.50         | 2              | 95           | 90
1          | Alice   | 7A    | 88.50         | 2              | 92           | 85
2          | Bob     | 7B    | 83.00         | 2              | 88           | 78
```

---

### DROP View:

```sql
DROP VIEW StudentPerformance;
```

---

## Finding Your Views

### In SQL Server Management Studio:
```
Object Explorer
  └── Databases
      └── YourDatabase
          └── Views
              └── (Refresh to see new views)
```

### Using SQL Query:
```sql
-- See all views in database
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.VIEWS
ORDER BY TABLE_SCHEMA, TABLE_NAME;
```

---

## Summary of Commands

### CREATE:
```sql
CREATE VIEW SchemaName.ViewName AS
SELECT ... FROM ... WHERE ...;
```

### ALTER:
```sql
ALTER VIEW SchemaName.ViewName AS
SELECT ... FROM ... WHERE ...;
```

### DROP:
```sql
DROP VIEW SchemaName.ViewName;
```

### Safe CREATE (with check):
```sql
IF OBJECT_ID('SchemaName.ViewName', 'V') IS NOT NULL
    DROP VIEW SchemaName.ViewName;
GO
CREATE VIEW SchemaName.ViewName AS
SELECT ... FROM ...;
GO
```

---

## Key Points

📌 **CREATE VIEW** = Make permanent virtual table  
📌 **ALTER VIEW** = Update view logic  
📌 **DROP VIEW** = Delete view  
📌 **Schema.ViewName** = Better organization  
📌 **Default schema** = dbo (if not specified)  
📌 **Refresh Object Explorer** = See new views  
📌 **Views don't store data** = Just store the query  

**Remember:** Views are like saving your favorite recipe - write it once, use it anytime! 👨‍🍳
