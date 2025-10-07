# SQL Tables: Complete Summary

## What Are Tables?

**Tables** are like spreadsheets or grids in your database that store actual data.

```
TABLE = Spreadsheet in Database
┌──────────────────────────────────────┐
│  student_id │ name   │ marks │ class │  ← Columns (Fields)
├─────────────┼────────┼───────┼───────┤
│  1          │ Aarav  │ 85    │ 7A    │  ← Row 1 (Record)
│  2          │ Diya   │ 92    │ 7B    │  ← Row 2
│  3          │ Rohan  │ 78    │ 7A    │  ← Row 3
└──────────────────────────────────────┘
```

**Key features:**
- **Columns** = Fields (like student_id, name)
- **Rows** = Records (each row is one student)
- **Cells** = Individual data pieces

---

## Two Types of Tables

### Type 1: Permanent Tables (Live Forever)

```
┌─────────────────────────────────┐
│  PERMANENT TABLE                 │
│  Created: 2025-01-01            │
│  Lives: FOREVER                 │
│  Deleted: Only when you DROP it │
└─────────────────────────────────┘
```

**Example:**
```sql
CREATE TABLE students (
    student_id INT,
    name VARCHAR(50),
    marks INT
);
```

✅ This table stays in database until you manually delete it!

---

### Type 2: Temporary Tables (Short Lifetime)

```
┌─────────────────────────────────┐
│  TEMPORARY TABLE                 │
│  Created: During session        │
│  Lives: Until session ends      │
│  Deleted: Automatically!        │
└─────────────────────────────────┘
```

**Example:**
```sql
SELECT * 
INTO #temp_students  -- Notice the # symbol!
FROM students;
```

❌ This table disappears when you close SQL client!

---

## Two Methods to Create Tables

### Method 1: CREATE + INSERT (From Scratch)

**Two separate steps:**

**Step 1: Define structure**
```sql
CREATE TABLE products (
    product_id INT,
    product_name VARCHAR(50),
    price DECIMAL(10,2)
);
```

**Step 2: Add data**
```sql
INSERT INTO products VALUES
(1, 'Laptop', 50000),
(2, 'Mouse', 500),
(3, 'Keyboard', 1500);
```

**Check result:**
```sql
SELECT * FROM products;
```

**Output:**
```
product_id | product_name | price
-----------|--------------|----------
1          | Laptop       | 50000.00
2          | Mouse        | 500.00
3          | Keyboard     | 1500.00
```

✅ **Use when:** Starting from scratch, need full control

---

### Method 2: CTAS (Create Table As Select)

**One step - creates table from query:**

```sql
-- Create table from existing data
CREATE TABLE high_price_products AS
SELECT 
    product_id,
    product_name,
    price,
    price * 0.10 AS tax_amount
FROM products
WHERE price > 1000;
```

**Check result:**
```sql
SELECT * FROM high_price_products;
```

**Output:**
```
product_id | product_name | price    | tax_amount
-----------|--------------|----------|------------
1          | Laptop       | 50000.00 | 5000.00
3          | Keyboard     | 1500.00  | 150.00
```

✅ **Use when:** Creating from existing data, need transformations

---

## Complete Comparison: All Table Types

### Setup: Source Data

```sql
-- Create source table (permanent)
CREATE TABLE student_scores (
    student_id INT,
    student_name VARCHAR(50),
    subject VARCHAR(30),
    marks INT
);

INSERT INTO student_scores VALUES
(1, 'Aarav', 'Math', 85),
(1, 'Aarav', 'Science', 90),
(2, 'Diya', 'Math', 92),
(2, 'Diya', 'Science', 88),
(3, 'Rohan', 'Math', 78),
(3, 'Rohan', 'Science', 82);
```

---

### Example 1: CREATE + INSERT (Permanent)

```sql
-- Method: CREATE + INSERT
CREATE TABLE student_summary (
    student_id INT,
    student_name VARCHAR(50),
    avg_marks DECIMAL(5,2),
    performance VARCHAR(20)
);

INSERT INTO student_summary
SELECT 
    student_id,
    student_name,
    AVG(marks) AS avg_marks,
    CASE 
        WHEN AVG(marks) >= 90 THEN 'Excellent'
        WHEN AVG(marks) >= 80 THEN 'Good'
        ELSE 'Average'
    END AS performance
FROM student_scores
GROUP BY student_id, student_name;
```

**Check result:**
```sql
SELECT * FROM student_summary;
```

**Output:**
```
student_id | student_name | avg_marks | performance
-----------|--------------|-----------|-------------
1          | Aarav        | 87.50     | Good
2          | Diya         | 90.00     | Excellent
3          | Rohan        | 80.00     | Good
```

✅ **This table is PERMANENT** - stays forever!

---

### Example 2: CTAS (Permanent)

```sql
-- Method: CTAS
CREATE TABLE top_performers AS
SELECT 
    student_name,
    AVG(marks) AS avg_marks,
    MAX(marks) AS best_score,
    MIN(marks) AS lowest_score
FROM student_scores
GROUP BY student_name
HAVING AVG(marks) >= 85;
```

**Check result:**
```sql
SELECT * FROM top_performers;
```

**Output:**
```
student_name | avg_marks | best_score | lowest_score
-------------|-----------|------------|-------------
Aarav        | 87.50     | 90         | 85
Diya         | 90.00     | 92         | 88
```

✅ **This table is also PERMANENT** - created in one step!

---

### Example 3: Temporary Table

```sql
-- Method: CTAS with temp table
SELECT 
    student_name,
    subject,
    marks,
    CASE 
        WHEN marks >= 90 THEN 'A'
        WHEN marks >= 80 THEN 'B'
        ELSE 'C'
    END AS grade
INTO #temp_grades  -- # makes it temporary!
FROM student_scores;
```

**Check result:**
```sql
SELECT * FROM #temp_grades;
```

**Output:**
```
student_name | subject | marks | grade
-------------|---------|-------|-------
Aarav        | Math    | 85    | B
Aarav        | Science | 90    | A
Diya         | Math    | 92    | A
Diya         | Science | 88    | B
Rohan        | Math    | 78    | C
Rohan        | Science | 82    | B
```

⏰ **This table is TEMPORARY** - disappears when you disconnect!

---

## Tables vs Views (When to Use CTAS Instead of Views)

### Scenario: Complex Dashboard Query

**Problem:** Complex query takes 10 seconds every time!

```sql
-- SLOW VIEW (runs complex query each time)
CREATE VIEW dashboard_view AS
SELECT 
    s.student_name,
    s.subject,
    s.marks,
    AVG(s.marks) OVER (PARTITION BY s.student_name) AS student_avg,
    AVG(s.marks) OVER (PARTITION BY s.subject) AS subject_avg,
    RANK() OVER (ORDER BY s.marks DESC) AS overall_rank
FROM student_scores s
JOIN (lots of complex joins and calculations...);

-- User queries: SLOW! ⏱️ (10 seconds each time)
SELECT * FROM dashboard_view;
```

---

**Solution:** Use CTAS for Performance!

```sql
-- Create physical table ONCE (takes 10 seconds)
CREATE TABLE dashboard_summary AS
SELECT 
    s.student_name,
    s.subject,
    s.marks,
    AVG(s.marks) OVER (PARTITION BY s.student_name) AS student_avg,
    AVG(s.marks) OVER (PARTITION BY s.subject) AS subject_avg,
    RANK() OVER (ORDER BY s.marks DESC) AS overall_rank
FROM student_scores s;

-- User queries: FAST! ⚡ (0.1 seconds)
SELECT * FROM dashboard_summary;
```

**Output (INSTANT!):**
```
student_name | subject | marks | student_avg | subject_avg | overall_rank
-------------|---------|-------|-------------|-------------|-------------
Diya         | Math    | 92    | 90.00       | 85.00       | 1
Aarav        | Science | 90    | 87.50       | 86.67       | 2
Diya         | Science | 88    | 90.00       | 86.67       | 3
Aarav        | Math    | 85    | 87.50       | 85.00       | 4
Rohan        | Science | 82    | 80.00       | 86.67       | 5
Rohan        | Math    | 78    | 80.00       | 85.00       | 6
```

**Performance comparison:**
- **View:** 10 seconds × 100 users = 1000 seconds ⏱️
- **CTAS:** 0.1 seconds × 100 users = 10 seconds ⚡

**100× FASTER!** 🚀

---

## Use Case: Data Snapshots for Bug Analysis

### Scenario: Data Quality Issue Discovered

```sql
-- Original table (data keeps changing)
CREATE TABLE daily_sales (
    sale_id INT,
    product VARCHAR(50),
    amount DECIMAL(10,2),
    sale_date DATE
);

INSERT INTO daily_sales VALUES
(1, 'Laptop', 50000, '2025-10-06'),
(2, 'Mouse', -500, '2025-10-06'),  -- BUG! Negative amount!
(3, 'Keyboard', 1500, '2025-10-06');
```

**Problem found at 10:00 AM:** "Negative amounts in database!"

---

**Take Snapshot Immediately with CTAS:**

```sql
-- Freeze the problematic data
CREATE TABLE sales_snapshot_bug_001 AS
SELECT 
    *,
    'Bug #001: Negative amounts' AS issue_description,
    GETDATE() AS snapshot_time
FROM daily_sales;
```

**Check snapshot:**
```sql
SELECT * FROM sales_snapshot_bug_001;
```

**Output:**
```
sale_id | product  | amount    | sale_date  | issue_description         | snapshot_time
--------|----------|-----------|------------|--------------------------|-------------------
1       | Laptop   | 50000.00  | 2025-10-06 | Bug #001: Negative amt   | 2025-10-06 10:00:00
2       | Mouse    | -500.00   | 2025-10-06 | Bug #001: Negative amt   | 2025-10-06 10:00:00
3       | Keyboard | 1500.00   | 2025-10-06 | Bug #001: Negative amt   | 2025-10-06 10:00:00
```

**Meanwhile, system fixes the bug:**
```sql
UPDATE daily_sales SET amount = 500 WHERE sale_id = 2;
```

**Now original table is fixed, but snapshot preserves the bug for analysis!**

```sql
-- Original (fixed)
SELECT * FROM daily_sales WHERE sale_id = 2;
```
**Output:** `amount = 500.00` ✅

```sql
-- Snapshot (preserves bug)
SELECT * FROM sales_snapshot_bug_001 WHERE sale_id = 2;
```
**Output:** `amount = -500.00` (bug preserved for analysis!)

---

## Use Case: Temporary Tables for Intermediate Results

### Scenario: ETL Process

```sql
-- STEP 1: Extract to temp table
SELECT *
INTO #raw_data
FROM source_system.orders;

-- STEP 2: Clean data
DELETE FROM #raw_data WHERE customer_name IS NULL;
UPDATE #raw_data SET status = 'Unknown' WHERE status IS NULL;

-- STEP 3: Transform
SELECT 
    customer_name,
    SUM(amount) AS total_spent,
    COUNT(*) AS order_count
INTO #customer_summary
FROM #raw_data
GROUP BY customer_name;

-- STEP 4: Load to warehouse
INSERT INTO warehouse.customer_facts
SELECT * FROM #customer_summary;

-- Close session → Both temp tables automatically deleted!
```

✅ **Advantage:** Automatic cleanup - no need to DROP tables manually!

---

## Quick Reference Table

| Aspect | Permanent Table | Temp Table | View |
|--------|----------------|------------|------|
| **Storage** | Physical data | Physical data (tempdb) | No data (virtual) |
| **Lifetime** | Forever | Session only | Forever |
| **Speed** | Fast | Fast | Slower |
| **Auto-cleanup** | ❌ No | ✅ Yes | N/A |
| **Data Freshness** | Static | Static | Always fresh |
| **Best For** | Final data | Intermediate work | Reusable logic |

---

## Decision Tree

```
Do you need to store data?
│
├─ YES, permanently
│  │
│  ├─ Creating from scratch?
│  │  └─ Use: CREATE + INSERT
│  │
│  └─ Creating from existing data?
│     └─ Use: CTAS
│
└─ YES, but temporarily (during session only)
   └─ Use: Temp Table (#table_name)
```

---

## Summary

### Tables Are:
- 📊 Like spreadsheets with rows and columns
- 💾 Store actual data physically
- 🏢 Foundation of databases

### Two Types:
- **Permanent:** Live forever ♾️
- **Temporary:** Disappear when session ends ⏰

### Two Creation Methods:
- **CREATE + INSERT:** Two steps, from scratch
- **CTAS:** One step, from query

### Key Use Cases:
- ⚡ **CTAS over Views:** When performance matters
- 📸 **CTAS for Snapshots:** Preserve data for bug analysis
- 🧹 **Temp Tables:** Intermediate results with auto-cleanup

**Remember:** Choose the right tool for your needs:
- Need speed? → CTAS table
- Need fresh data? → View
- Need temporary work? → Temp table
- Starting fresh? → CREATE + INSERT

Tables are the heart of your database - master them and you'll master SQL! 💪
