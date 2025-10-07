# SQL: Temporary Tables - Complete Syntax & Examples

## The Easiest Syntax Ever! 🎉

Creating a temporary table is **super simple** - just add a **#** (hash/pound symbol) before the table name!

---

## Basic Syntax

### Regular Permanent Table
```sql
SELECT * 
INTO table_name      -- Creates PERMANENT table
FROM source_table;
```

### Temporary Table (Just add #!)
```sql
SELECT * 
INTO #table_name     -- Creates TEMPORARY table (notice the #)
FROM source_table;
```

**That's it!** The `#` symbol tells SQL Server: "This is temporary, delete it when I'm done!"

---

## Where Are Temp Tables Stored?

### Database Structure in SQL Server

```
SQL Server
├── User Databases
│   ├── sales_db (your regular database)
│   │   ├── customers table
│   │   ├── orders table
│   │   └── products table
│   
└── System Databases (managed by SQL Server)
    ├── master
    ├── model
    ├── msdb
    └── tempdb ← TEMP TABLES STORED HERE!
        └── Temporary Tables
            ├── #temp_table1
            └── #temp_table2
```

**Key Point:** You don't manually go to tempdb - SQL Server handles it automatically!

---

## Complete Real-World Example: Order Management

### Step 1: Setup - Create Original Table

```sql
-- Create permanent orders table
CREATE TABLE orders (
    order_id INT,
    customer_name VARCHAR(50),
    product VARCHAR(50),
    quantity INT,
    price DECIMAL(10,2),
    order_status VARCHAR(20)
);

-- Insert sample data
INSERT INTO orders VALUES
(1, 'Amit Kumar', 'Laptop', 1, 50000, 'Delivered'),
(2, 'Priya Singh', 'Mouse', 2, 500, 'Shipped'),
(3, 'Rohan Patel', 'Keyboard', 1, 1500, 'Delivered'),
(4, 'Diya Reddy', 'Monitor', 1, 15000, 'Shipped'),
(5, 'Arjun Sharma', 'Headphones', 3, 2000, 'Delivered'),
(6, 'Sneha Iyer', 'Webcam', 1, 3000, 'Shipped'),
(7, 'Rajesh Kumar', 'Tablet', 1, 25000, 'Delivered'),
(8, 'Ananya Singh', 'Charger', 2, 800, 'Shipped'),
(9, 'Vikram Reddy', 'USB Drive', 5, 500, 'Delivered'),
(10, 'Meera Patel', 'Hard Drive', 1, 5000, 'Shipped');
```

**Check the original table:**
```sql
SELECT * FROM orders;
```

**Output:**
```
order_id | customer_name  | product     | quantity | price    | order_status
---------|----------------|-------------|----------|----------|-------------
1        | Amit Kumar     | Laptop      | 1        | 50000.00 | Delivered
2        | Priya Singh    | Mouse       | 2        | 500.00   | Shipped
3        | Rohan Patel    | Keyboard    | 1        | 1500.00  | Delivered
4        | Diya Reddy     | Monitor     | 1        | 15000.00 | Shipped
5        | Arjun Sharma   | Headphones  | 3        | 2000.00  | Delivered
6        | Sneha Iyer     | Webcam      | 1        | 3000.00  | Shipped
7        | Rajesh Kumar   | Tablet      | 1        | 25000.00 | Delivered
8        | Ananya Singh   | Charger     | 2        | 800.00   | Shipped
9        | Vikram Reddy   | USB Drive   | 5        | 500.00   | Delivered
10       | Meera Patel    | Hard Drive  | 1        | 5000.00  | Shipped
```

---

### Step 2: Create Temporary Copy

**Why?** You want to experiment without touching the original data!

```sql
-- Create temp table with ALL data from orders
SELECT *
INTO #orders          -- Notice the # symbol!
FROM orders;
```

**What happened:**
- ✅ Query executed: Selected all data from `orders`
- ✅ Created temporary table named `#orders`
- ✅ Copied all 10 rows to temp table
- ✅ Stored in tempdb (automatically)

**Output message:**
```
(10 rows affected)
```

---

### Step 3: Verify Temp Table Was Created

```sql
-- Query the temp table (not the original!)
SELECT * FROM #orders;
```

**Output:**
```
order_id | customer_name  | product     | quantity | price    | order_status
---------|----------------|-------------|----------|----------|-------------
1        | Amit Kumar     | Laptop      | 1        | 50000.00 | Delivered
2        | Priya Singh    | Mouse       | 2        | 500.00   | Shipped
3        | Rohan Patel    | Keyboard    | 1        | 1500.00  | Delivered
4        | Diya Reddy     | Monitor     | 1        | 15000.00 | Shipped
5        | Arjun Sharma   | Headphones  | 3        | 2000.00  | Delivered
6        | Sneha Iyer     | Webcam      | 1        | 3000.00  | Shipped
7        | Rajesh Kumar   | Tablet      | 1        | 25000.00 | Delivered
8        | Ananya Singh   | Charger     | 2        | 800.00   | Shipped
9        | Vikram Reddy   | USB Drive   | 5        | 500.00   | Delivered
10       | Meera Patel    | Hard Drive  | 1        | 5000.00  | Shipped
```

✅ **Perfect!** Exact copy in temporary storage!

---

### Step 4: Experiment with Temp Table (Safe Playground!)

**Goal:** Remove all delivered orders (just for analysis)

```sql
-- Delete from TEMP table (not original!)
DELETE FROM #orders
WHERE order_status = 'Delivered';
```

**Output message:**
```
(5 rows affected)
```

**Check what's left:**
```sql
SELECT * FROM #orders;
```

**Output:**
```
order_id | customer_name | product    | quantity | price    | order_status
---------|---------------|------------|----------|----------|-------------
2        | Priya Singh   | Mouse      | 2        | 500.00   | Shipped
4        | Diya Reddy    | Monitor    | 1        | 15000.00 | Shipped
6        | Sneha Iyer    | Webcam     | 1        | 3000.00  | Shipped
8        | Ananya Singh  | Charger    | 2        | 800.00   | Shipped
10       | Meera Patel   | Hard Drive | 1        | 5000.00  | Shipped
```

✅ **Only "Shipped" orders remain in temp table!**

---

### Step 5: Original Table Still Safe!

```sql
-- Check the ORIGINAL table
SELECT * FROM orders;
```

**Output:**
```
order_id | customer_name  | product     | quantity | price    | order_status
---------|----------------|-------------|----------|----------|-------------
1        | Amit Kumar     | Laptop      | 1        | 50000.00 | Delivered
2        | Priya Singh    | Mouse       | 2        | 500.00   | Shipped
3        | Rohan Patel    | Keyboard    | 1        | 1500.00  | Delivered
4        | Diya Reddy     | Monitor     | 1        | 15000.00 | Shipped
5        | Arjun Sharma   | Headphones  | 3        | 2000.00  | Delivered
6        | Sneha Iyer     | Webcam      | 1        | 3000.00  | Shipped
7        | Rajesh Kumar   | Tablet      | 1        | 25000.00 | Delivered
8        | Ananya Singh   | Charger     | 2        | 800.00   | Shipped
9        | Vikram Reddy   | USB Drive   | 5        | 500.00   | Delivered
10       | Meera Patel    | Hard Drive  | 1        | 5000.00  | Shipped
```

🎉 **Original table is UNCHANGED!** All 10 rows still there with delivered orders intact!

---

### Step 6: Do More Analysis on Temp Table

```sql
-- Calculate total value of remaining shipped orders
SELECT 
    COUNT(*) AS total_orders,
    SUM(quantity * price) AS total_value
FROM #orders;
```

**Output:**
```
total_orders | total_value
-------------|-------------
5            | 20600.00
```

```sql
-- Find high-value shipped orders
SELECT customer_name, product, price
FROM #orders
WHERE price > 3000
ORDER BY price DESC;
```

**Output:**
```
customer_name | product    | price
--------------|------------|----------
Diya Reddy    | Monitor    | 15000.00
Meera Patel   | Hard Drive | 5000.00
```

---

### Step 7: Save Results to Permanent Table

**Situation:** "I like these results! I want to keep them permanently!"

```sql
-- Copy temp table results to NEW permanent table
SELECT *
INTO orders_test      -- No # symbol = PERMANENT table
FROM #orders;
```

**Output message:**
```
(5 rows affected)
```

**Check the new permanent table:**
```sql
SELECT * FROM orders_test;
```

**Output:**
```
order_id | customer_name | product    | quantity | price    | order_status
---------|---------------|------------|----------|----------|-------------
2        | Priya Singh   | Mouse      | 2        | 500.00   | Shipped
4        | Diya Reddy    | Monitor    | 1        | 15000.00 | Shipped
6        | Sneha Iyer    | Webcam     | 1        | 3000.00  | Shipped
8        | Ananya Singh  | Charger    | 2        | 800.00   | Shipped
10       | Meera Patel   | Hard Drive | 1        | 5000.00  | Shipped
```

✅ **Now you have THREE tables:**
1. `orders` (original, untouched, 10 rows)
2. `#orders` (temp table, 5 rows, will be deleted when session ends)
3. `orders_test` (permanent, 5 rows, stays forever)

---

### Step 8: End Session - Watch Temp Table Disappear!

**Close your SQL client and reconnect (new session starts)**

```sql
-- Try to query the temp table
SELECT * FROM #orders;
```

**Output:**
```
ERROR: Invalid object name '#orders'
```

❌ **Temp table is GONE!** SQL Server automatically deleted it.

**But check the permanent tables:**
```sql
-- Original table still exists
SELECT COUNT(*) FROM orders;
```
**Output:** `10`

```sql
-- New permanent table still exists
SELECT COUNT(*) FROM orders_test;
```
**Output:** `5`

✅ **Permanent tables survived the session end!**

---

## Visual Workflow Summary

```
┌─────────────────────────────────────────────────────────┐
│  SESSION STARTS                                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  1. SELECT * INTO #orders FROM orders                   │
│     → Creates temp copy (10 rows)                       │
│                                                          │
│  2. DELETE FROM #orders WHERE status = 'Delivered'      │
│     → Removes 5 rows from temp (5 left)                 │
│     → Original 'orders' table still has 10 rows!        │
│                                                          │
│  3. Do analysis on #orders                              │
│     → Safe to experiment!                               │
│                                                          │
│  4. SELECT * INTO orders_test FROM #orders              │
│     → Saves results to permanent table                  │
│                                                          │
├─────────────────────────────────────────────────────────┤
│  SESSION ENDS (close SQL client)                        │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  Result:                                                │
│  ✅ orders (10 rows) - Still exists                     │
│  ✅ orders_test (5 rows) - Still exists                 │
│  ❌ #orders - DELETED automatically                     │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## More Examples

### Example 1: Create Temp Table with Calculations

```sql
-- Create temp table with additional calculated columns
SELECT 
    order_id,
    customer_name,
    product,
    quantity,
    price,
    quantity * price AS total_amount,
    order_status
INTO #orders_with_totals
FROM orders;

-- Check it
SELECT * FROM #orders_with_totals
ORDER BY total_amount DESC;
```

**Output:**
```
order_id | customer_name | product    | quantity | price    | total_amount | order_status
---------|---------------|------------|----------|----------|--------------|-------------
1        | Amit Kumar    | Laptop     | 1        | 50000.00 | 50000.00     | Delivered
7        | Rajesh Kumar  | Tablet     | 1        | 25000.00 | 25000.00     | Delivered
4        | Diya Reddy    | Monitor    | 1        | 15000.00 | 15000.00     | Shipped
5        | Arjun Sharma  | Headphones | 3        | 2000.00  | 6000.00      | Delivered
...
```

---

### Example 2: Create Temp Table with Filtered Data

```sql
-- Create temp table with only high-value orders
SELECT *
INTO #high_value_orders
FROM orders
WHERE price > 10000;

-- Count high-value orders
SELECT COUNT(*) AS high_value_count FROM #high_value_orders;
```

**Output:**
```
high_value_count
----------------
3
```

---

### Example 3: Multiple Temp Tables in One Session

```sql
-- Temp table 1: Shipped orders
SELECT *
INTO #shipped_orders
FROM orders
WHERE order_status = 'Shipped';

-- Temp table 2: Delivered orders
SELECT *
INTO #delivered_orders
FROM orders
WHERE order_status = 'Delivered';

-- Compare counts
SELECT 
    (SELECT COUNT(*) FROM #shipped_orders) AS shipped_count,
    (SELECT COUNT(*) FROM #delivered_orders) AS delivered_count;
```

**Output:**
```
shipped_count | delivered_count
--------------|----------------
5             | 5
```

**All temp tables exist simultaneously in your session!**

---

## Important Rules

### ✅ DO's
- Use `#` before table name for temp tables
- Experiment freely with temp tables
- Create multiple temp tables in one session
- Use temp tables for complex analysis
- Save important results to permanent tables

### ❌ DON'Ts
- Don't forget the `#` symbol (or it becomes permanent!)
- Don't expect temp tables to survive session end
- Don't share temp table names with other users (they can't see yours)
- Don't use temp tables for long-term storage

---

## Quick Reference

| Operation | Syntax |
|-----------|--------|
| **Create temp table** | `SELECT * INTO #temp_name FROM table` |
| **Query temp table** | `SELECT * FROM #temp_name` |
| **Update temp table** | `UPDATE #temp_name SET column = value` |
| **Delete from temp** | `DELETE FROM #temp_name WHERE condition` |
| **Save to permanent** | `SELECT * INTO perm_name FROM #temp_name` |
| **Drop temp table** | `DROP TABLE #temp_name` (or close session) |

---

## Summary

**Temporary Tables with # Symbol:**
- 🎯 Super easy: Just add `#` before table name
- 🗑️ Auto-deleted when session ends
- 🔒 Private to your session
- 🧪 Perfect for experimentation
- 💾 Can save results to permanent tables
- 🏢 Stored in system database (tempdb)

**Think of it as:**
- 📝 Rough notebook vs final notebook
- 🎨 Practice canvas vs final painting
- 🧪 Lab experiment vs published results
- 🏗️ Construction site vs finished building

**Remember:** Temp tables are your **safe playground** where you can experiment without fear of breaking anything!
