# SQL: Introduction to Temporary Tables (Temp Tables)

## What Are Temporary Tables?

Imagine you're doing homework:
- **Permanent Tables** = Your final notebook that stays in your bag forever
- **Temporary Tables** = Rough paper you use for calculations and throw away when done

**Temporary tables** are like scratch paper for your database - they exist while you work and disappear when you're finished!

---

## Understanding the Difference

### Permanent Tables
```
You create table → It stays FOREVER → You must manually delete it
```

### Temporary Tables (Temp Tables)
```
You create table → Use during session → Automatically deleted when session ends
```

---

## What is a "Session"?

**Session = Time between connecting and disconnecting from database**

```
┌─────────────────────────────────────────────┐
│  SESSION TIMELINE                            │
├─────────────────────────────────────────────┤
│  9:00 AM  → Open SQL tool, connect to DB    │ ← Session STARTS
│  9:15 AM  → Create temp table               │
│  9:30 AM  → Use temp table for analysis     │
│  10:00 AM → Still using temp table          │
│  11:00 AM → Close SQL tool, disconnect      │ ← Session ENDS
│                                              │
│  Result: Temp table is DELETED automatically│
└─────────────────────────────────────────────┘
```

**Think of it like:**
- Opening a book = Starting session
- Using bookmark = Creating temp table
- Closing book = Ending session (bookmark falls out!)

---

## Creating Temporary Tables

### Syntax (varies by database)

**SQL Server:**
```sql
-- Notice the # symbol (single # for session-level temp table)
CREATE TABLE #temp_table_name (
    column1 datatype,
    column2 datatype
);
```

**MySQL:**
```sql
CREATE TEMPORARY TABLE temp_table_name (
    column1 datatype,
    column2 datatype
);
```

**PostgreSQL:**
```sql
CREATE TEMP TABLE temp_table_name (
    column1 datatype,
    column2 datatype
);
```

---

## Example 1: Simple Temporary Table

### Setup: Original Data

```sql
-- Permanent table with student data
CREATE TABLE students (
    student_id INT,
    student_name VARCHAR(50),
    class VARCHAR(10),
    marks INT
);

INSERT INTO students VALUES
(1, 'Aarav', 'Class 7', 85),
(2, 'Diya', 'Class 7', 92),
(3, 'Rohan', 'Class 7', 78),
(4, 'Priya', 'Class 8', 88),
(5, 'Arjun', 'Class 8', 95);
```

---

### Create Temporary Table (SQL Server Syntax)

```sql
-- Create temp table with high scorers
CREATE TABLE #high_scorers (
    student_id INT,
    student_name VARCHAR(50),
    marks INT
);

-- Insert data into temp table
INSERT INTO #high_scorers
SELECT student_id, student_name, marks
FROM students
WHERE marks >= 85;
```

**Check the temp table:**
```sql
SELECT * FROM #high_scorers;
```

**Output:**
```
student_id | student_name | marks
-----------|--------------|-------
1          | Aarav        | 85
2          | Diya         | 92
4          | Priya        | 88
5          | Arjun        | 95
```

✅ **Temp table works just like a normal table!**

---

### Use the Temp Table

```sql
-- Calculate average of high scorers
SELECT AVG(marks) AS average_marks
FROM #high_scorers;
```

**Output:**
```
average_marks
-------------
90.00
```

```sql
-- Find top scorer
SELECT student_name, marks
FROM #high_scorers
ORDER BY marks DESC
LIMIT 1;
```

**Output:**
```
student_name | marks
-------------|-------
Arjun        | 95
```

---

### Session Ends = Table Disappears!

```sql
-- Close your SQL client and disconnect
-- Wait a few minutes...
-- Reconnect and try to query the temp table

SELECT * FROM #high_scorers;
```

**Output:**
```
ERROR: Table '#high_scorers' does not exist
```

❌ **Table automatically deleted!** The database cleaned it up when you disconnected.

---

## Example 2: Temp Table Using CTAS Pattern

### Create Temp Table from Query (SQL Server)

```sql
-- Create temp table with complex calculations
SELECT 
    student_id,
    student_name,
    marks,
    marks * 1.1 AS bonus_marks,
    CASE 
        WHEN marks >= 90 THEN 'A+'
        WHEN marks >= 85 THEN 'A'
        WHEN marks >= 80 THEN 'B'
        ELSE 'C'
    END AS grade
INTO #student_performance
FROM students;
```

**Check the temp table:**
```sql
SELECT * FROM #student_performance;
```

**Output:**
```
student_id | student_name | marks | bonus_marks | grade
-----------|--------------|-------|-------------|-------
1          | Aarav        | 85    | 93.5        | A
2          | Diya         | 92    | 101.2       | A+
3          | Rohan        | 78    | 85.8        | C
4          | Priya        | 88    | 96.8        | A
5          | Arjun        | 95    | 104.5       | A+
```

---

### MySQL Syntax (Different!)

```sql
-- MySQL uses TEMPORARY keyword
CREATE TEMPORARY TABLE student_performance AS
SELECT 
    student_id,
    student_name,
    marks,
    marks * 1.1 AS bonus_marks,
    CASE 
        WHEN marks >= 90 THEN 'A+'
        WHEN marks >= 85 THEN 'A'
        WHEN marks >= 80 THEN 'B'
        ELSE 'C'
    END AS grade
FROM students;
```

---

## Real-World Example: Multi-Step Analysis

### Scenario: Teacher Needs Complex Report

**Problem:** Need to analyze student performance across multiple criteria, but calculation is complex.

```sql
-- STEP 1: Create temp table with base calculations
CREATE TABLE #step1_base_data (
    student_id INT,
    student_name VARCHAR(50),
    total_marks INT
);

INSERT INTO #step1_base_data
SELECT 
    student_id,
    student_name,
    marks
FROM students
WHERE marks > 0;

-- Check step 1
SELECT * FROM #step1_base_data;
```

**Output:**
```
student_id | student_name | total_marks
-----------|--------------|-------------
1          | Aarav        | 85
2          | Diya         | 92
3          | Rohan        | 78
4          | Priya        | 88
5          | Arjun        | 95
```

---

```sql
-- STEP 2: Create another temp table with rankings
SELECT 
    student_id,
    student_name,
    total_marks,
    CASE 
        WHEN total_marks >= 90 THEN 1
        WHEN total_marks >= 80 THEN 2
        ELSE 3
    END AS performance_rank
INTO #step2_ranked_data
FROM #step1_base_data;

-- Check step 2
SELECT * FROM #step2_ranked_data;
```

**Output:**
```
student_id | student_name | total_marks | performance_rank
-----------|--------------|-------------|------------------
1          | Aarav        | 85          | 2
2          | Diya         | 92          | 1
3          | Rohan        | 78          | 3
4          | Priya        | 88          | 2
5          | Arjun        | 95          | 1
```

---

```sql
-- STEP 3: Final report using temp tables
SELECT 
    performance_rank,
    COUNT(*) AS student_count,
    AVG(total_marks) AS average_marks
FROM #step2_ranked_data
GROUP BY performance_rank
ORDER BY performance_rank;
```

**Output:**
```
performance_rank | student_count | average_marks
-----------------|---------------|---------------
1                | 2             | 93.50
2                | 2             | 86.50
3                | 1             | 78.00
```

✅ **All three temp tables used together for complex analysis!**

**When session ends:** All three temp tables (#step1_base_data, #step2_ranked_data) automatically deleted!

---

## Permanent vs Temporary Tables Comparison

| Feature | Permanent Tables | Temporary Tables |
|---------|-----------------|------------------|
| **Lifetime** | Forever (until manually dropped) | Session only |
| **Automatic Cleanup** | ❌ No | ✅ Yes |
| **Visible to Others** | ✅ Yes (all users) | ❌ No (only you) |
| **Survives Disconnect** | ✅ Yes | ❌ No |
| **Storage** | Permanent disk space | Temporary storage |
| **Use Case** | Long-term data | Intermediate calculations |
| **Symbol (SQL Server)** | No special symbol | # prefix |

---

## Visualizing the Lifecycle

### Permanent Table Lifecycle
```
┌────────────────────────────────────────┐
│  CREATE TABLE students                  │
│  ↓                                      │
│  Table created in database              │
│  ↓                                      │
│  Session 1: Use table ✓                │
│  Close connection                       │
│  ↓                                      │
│  Session 2: Use table ✓ (still exists!)│
│  Close connection                       │
│  ↓                                      │
│  Session 3: Use table ✓ (still exists!)│
│  ↓                                      │
│  Must manually DROP TABLE               │
└────────────────────────────────────────┘
```

### Temporary Table Lifecycle
```
┌────────────────────────────────────────┐
│  Session 1 STARTS                       │
│  ↓                                      │
│  CREATE TABLE #temp_data                │
│  ↓                                      │
│  Use #temp_data ✓                      │
│  More queries with #temp_data ✓        │
│  ↓                                      │
│  Session 1 ENDS (close connection)      │
│  ↓                                      │
│  💥 #temp_data DELETED automatically    │
│  ↓                                      │
│  Session 2 STARTS                       │
│  ↓                                      │
│  Try SELECT * FROM #temp_data           │
│  ❌ ERROR: Table doesn't exist          │
└────────────────────────────────────────┘
```

---

## Practical Example: Shopping Cart Analysis

```sql
-- Original orders table (permanent)
CREATE TABLE orders (
    order_id INT,
    customer_name VARCHAR(50),
    product VARCHAR(50),
    price DECIMAL(10,2),
    quantity INT
);

INSERT INTO orders VALUES
(1, 'Amit', 'Laptop', 50000, 1),
(2, 'Amit', 'Mouse', 500, 2),
(3, 'Priya', 'Keyboard', 1500, 1),
(4, 'Amit', 'Monitor', 15000, 1),
(5, 'Rohan', 'Headphones', 2000, 3);
```

---

### Create Temp Table for Today's Analysis

```sql
-- Create temp table for customer totals
CREATE TABLE #customer_totals (
    customer_name VARCHAR(50),
    total_items INT,
    total_spent DECIMAL(10,2)
);

-- Calculate and insert
INSERT INTO #customer_totals
SELECT 
    customer_name,
    SUM(quantity) AS total_items,
    SUM(price * quantity) AS total_spent
FROM orders
GROUP BY customer_name;

-- View results
SELECT * FROM #customer_totals;
```

**Output:**
```
customer_name | total_items | total_spent
--------------|-------------|-------------
Amit          | 4           | 66000.00
Priya         | 1           | 1500.00
Rohan         | 3           | 6000.00
```

---

### Use Temp Table for Further Analysis

```sql
-- Find high-value customers
SELECT customer_name, total_spent
FROM #customer_totals
WHERE total_spent > 5000
ORDER BY total_spent DESC;
```

**Output:**
```
customer_name | total_spent
--------------|-------------
Amit          | 66000.00
Rohan         | 6000.00
```

**When you close the application:** #customer_totals is automatically deleted!

---

## When to Use Temporary Tables

### ✅ Use Temp Tables When:
- 🧮 Doing complex multi-step calculations
- 🔍 Breaking down complex queries into steps
- 💾 Need intermediate storage during analysis
- 🚫 Don't want to clutter database with extra tables
- 👤 Results only needed by you, during current session
- 🧹 Want automatic cleanup

### ❌ Don't Use Temp Tables When:
- 💾 Need to save results permanently
- 👥 Other users need access to the data
- 🔄 Need data to survive beyond current session
- 📊 Creating reports that run later

---

## Summary

**Temporary Tables:**
- 📝 Work exactly like permanent tables
- ⏰ Exist only during your session
- 🗑️ Automatically deleted when you disconnect
- 🔒 Private to your session (others can't see)
- 🧹 No manual cleanup needed
- 💡 Perfect for intermediate calculations

**Think of them as:**
- 📋 Sticky notes (vs permanent notebooks)
- 🍕 Paper plates (vs china dishes)
- 📄 Draft paper (vs final report)
- 🧊 Ice cubes (melts away when done!)

**Key Difference from Permanent Tables:**
```
Permanent: Create → Lives FOREVER → Must manually DROP
Temporary: Create → Lives DURING SESSION → Auto-deleted
```

**Remember:** Temp tables are your database's way of giving you scratch space that cleans itself up automatically!
