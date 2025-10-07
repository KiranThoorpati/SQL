# How Database Executes Views Behind the Scenes
*Understanding What Happens When You Use a View*

## The Big Picture: What Happens Step by Step

Think of a view like a **recipe card** in a cookbook:
- The recipe (view) is stored in the cookbook (database catalog)
- When you want to cook (query the view), you read the recipe
- The actual ingredients (data) come from the pantry (tables)
- The recipe card itself has no food - just instructions!

---

## Visual Flow Diagram

```
USER: "Give me data from TopCustomers view"
    ↓
DATABASE ENGINE: "Let me check what TopCustomers view does"
    ↓
SYSTEM CATALOG: "Here's the query stored in the view"
    ↓
DATABASE ENGINE: "Now I'll execute that query"
    ↓
PHYSICAL TABLE (Orders): "Here's the actual data"
    ↓
DATABASE ENGINE: "Got the data! Sending to user"
    ↓
USER: "Thanks! Here's my result"
```

---

## Step-by-Step: What Happens Inside

### Step 1: Creating a View

**What the user does:**
```sql
CREATE VIEW Sales.TopCustomers AS
SELECT 
    customer_id,
    customer_name,
    SUM(order_amount) as total_sales
FROM Sales.Orders
GROUP BY customer_id, customer_name
HAVING SUM(order_amount) > 10000;
```

**What the database does:**

```
DATABASE ENGINE receives: CREATE VIEW command
    ↓
Goes to SYSTEM CATALOG (on disk)
    ↓
Stores TWO things:
  1. METADATA (view name, columns, schema)
  2. SQL QUERY (the SELECT statement)
    ↓
Does NOT create any table
Does NOT store any data
    ↓
Returns: "View created successfully!"
```

---

### Step 2: Querying the View

**What the user does:**
```sql
SELECT * FROM Sales.TopCustomers;
```

**What the database does:**

```
DATABASE ENGINE receives: Query for TopCustomers
    ↓
Recognizes: "This is a VIEW, not a TABLE"
    ↓
Goes to SYSTEM CATALOG
    ↓
Retrieves: The SQL query stored in the view
    ↓
Executes the view's query:
  - Accesses physical table: Sales.Orders
  - Runs: SUM, GROUP BY, HAVING
  - Gets actual data from disk
    ↓
Takes the view's results
    ↓
Applies user's query on top
    ↓
Returns final results to user
```

---

### Step 3: Dropping the View

**What the user does:**
```sql
DROP VIEW Sales.TopCustomers;
```

**What the database does:**

```
DATABASE ENGINE receives: DROP VIEW command
    ↓
Goes to SYSTEM CATALOG
    ↓
Deletes:
  1. View metadata
  2. View SQL query
    ↓
Does NOT touch:
  - Physical tables
  - Actual data
    ↓
Returns: "View dropped successfully!"
```

**Important:** Dropping a view does NOT delete any data! ✅

---

## Complete Working Example

### Setup: Create Tables and Data

```sql
-- Create database
CREATE DATABASE CompanyDB;
USE CompanyDB;

-- Create schema
CREATE SCHEMA Sales;

-- Create physical table (stores actual data)
CREATE TABLE Sales.Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    customer_name VARCHAR(50),
    order_date DATE,
    order_amount DECIMAL(10,2)
);

-- Insert actual data (stored on disk)
INSERT INTO Sales.Orders VALUES
(1, 101, 'Alice Corp', '2024-01-15', 5000.00),
(2, 101, 'Alice Corp', '2024-02-20', 7000.00),
(3, 102, 'Bob Ltd', '2024-01-10', 3000.00),
(4, 103, 'Charlie Inc', '2024-03-05', 15000.00),
(5, 103, 'Charlie Inc', '2024-03-15', 8000.00),
(6, 104, 'Diana LLC', '2024-02-28', 2000.00);
```

---

## Example 1: Creating a View

### Create the View:

```sql
-- Data Engineer creates view
CREATE VIEW Sales.TopCustomers AS
SELECT 
    customer_id,
    customer_name,
    SUM(order_amount) as total_sales,
    COUNT(order_id) as order_count
FROM Sales.Orders
GROUP BY customer_id, customer_name
HAVING SUM(order_amount) > 10000;
```

**What gets stored in SYSTEM CATALOG:**
```
View Metadata:
- Name: TopCustomers
- Schema: Sales
- Columns: customer_id, customer_name, total_sales, order_count

View Query:
- SELECT customer_id, customer_name, 
  SUM(order_amount) as total_sales,
  COUNT(order_id) as order_count
  FROM Sales.Orders
  GROUP BY customer_id, customer_name
  HAVING SUM(order_amount) > 10000
```

**What gets stored in USER DATA:**
- ❌ NOTHING! No data stored!

---

## Example 2: Querying the View

### User's Simple Query:

```sql
-- Data Analyst queries the view
SELECT * FROM Sales.TopCustomers;
```

**Behind the Scenes (Step by Step):**

**Step 1:** Database sees "TopCustomers"
```
Database: "Is this a table or view?"
Catalog: "It's a view!"
```

**Step 2:** Database retrieves view's query
```
Database: "What query should I run?"
Catalog: "Here's the query: SELECT customer_id..."
```

**Step 3:** Database executes view's query
```
Database: "Running view query on Sales.Orders table"
From Orders table on disk:
- Gets all 6 rows
- Groups by customer
- Calculates SUM(order_amount)
- Filters: HAVING > 10000
```

**Step 4:** View's query produces result
```
Temporary Result:
customer_id | customer_name | total_sales | order_count
------------|---------------|-------------|------------
101         | Alice Corp    | 12000.00    | 2
103         | Charlie Inc   | 23000.00    | 2
```

**Step 5:** User's query runs on view's result
```
User wants: SELECT * FROM TopCustomers
Database applies user's query to the temporary result
```

**Final Output:**
```
customer_id | customer_name | total_sales | order_count
------------|---------------|-------------|------------
101         | Alice Corp    | 12000.00    | 2
103         | Charlie Inc   | 23000.00    | 2
```

---

## Example 3: Complex Query on View

### User Adds Filtering:

```sql
-- User adds WHERE clause
SELECT 
    customer_name,
    total_sales
FROM Sales.TopCustomers
WHERE total_sales > 15000;
```

**What Happens:**

**Step 1:** Database executes view's query (same as before)
```
View produces:
- Alice Corp: 12000
- Charlie Inc: 23000
```

**Step 2:** User's WHERE clause filters view's results
```
Database applies: WHERE total_sales > 15000
Keeps only: Charlie Inc (23000 > 15000)
Drops: Alice Corp (12000 < 15000)
```

**Final Output:**
```
customer_name | total_sales
--------------|------------
Charlie Inc   | 23000.00
```

---

## What's Stored Where?

### SYSTEM CATALOG (on disk):
```
✅ View name and metadata
✅ View's SQL query
❌ NO actual data
```

### USER DATA (on disk):
```
✅ Physical table data (Orders)
❌ NO view data
```

### CACHE (memory):
```
⚡ Temporary results during query execution
🗑️ Deleted after query completes
```

---

## Comparing Table vs View Storage

### Physical Table (Orders):

**What's stored:**
```
SYSTEM CATALOG:
- Table name: Orders
- Column definitions
- Data types
- Constraints

USER DATA:
- Actual rows of data ✅
- All 6 orders stored permanently
```

---

### View (TopCustomers):

**What's stored:**
```
SYSTEM CATALOG:
- View name: TopCustomers
- Column definitions
- SQL query ✅

USER DATA:
- Nothing! ❌
- Data comes from Orders table when queried
```

---

## Dropping Views vs Dropping Tables

### Dropping a View (SAFE):

```sql
DROP VIEW Sales.TopCustomers;
```

**What gets deleted:**
- ✅ View metadata from catalog
- ✅ View SQL query from catalog
- ❌ NO data deleted (didn't store any!)

**Result:** Orders table still has all 6 rows! ✅

---

### Dropping a Table (DANGEROUS):

```sql
DROP TABLE Sales.Orders;
```

**What gets deleted:**
- ✅ Table metadata from catalog
- ✅ ALL actual data! ⚠️
- ✅ All 6 rows permanently deleted!

**Result:** Data is GONE forever! ❌

---

## Real-World Analogy

### Library System:

**Physical Table = Book**
- Contains actual pages and content
- Stored on bookshelf
- If you destroy it, content is lost forever

**View = Library Catalog Card**
- Says where to find the book
- Says what's in the book
- If you destroy the card, book is still safe!

---

## Performance Impact

### Querying a Table (Fast):
```
User → Database → Table (data ready) → User
One step! ⚡
```

### Querying a View (Slightly Slower):
```
User → Database → Catalog (get query) → Execute query → Table (get data) → User
Two steps! 🐌
```

---

## Summary of What's Stored

| Component | Table | View |
|-----------|-------|------|
| **Metadata** | ✅ Stored | ✅ Stored |
| **SQL Query** | ❌ Not stored | ✅ Stored |
| **Actual Data** | ✅ Stored | ❌ Not stored |
| **Location** | User Data area | System Catalog only |
| **If dropped** | Data LOST! | Data safe! |

---

## Key Takeaways

📌 **View creation** = Stores query in catalog, NO data  
📌 **View query** = Executes saved query, gets data from tables  
📌 **View storage** = Catalog only (metadata + query)  
📌 **Dropping view** = Deletes query, data stays safe  
📌 **Two-step process** = View query first, then user query  
📌 **Data source** = Always from physical tables  
📌 **Safe to drop** = Views don't contain actual data  

**Remember:** A view is like a window - you can look through it to see the data, but the data isn't stored in the window itself! 🪟
