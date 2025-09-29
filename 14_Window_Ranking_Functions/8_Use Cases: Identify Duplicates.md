# ROW_NUMBER Real-World Use Cases

## Why ROW_NUMBER is Most Used

According to real project experience, **ROW_NUMBER is used much more often** than RANK or DENSE_RANK!

Let's learn the most important use cases with step-by-step examples.

---

## Use Case 1: Top-N Analysis

**What is it?** Finding the best performers or highest values

**Business Question:** "Show me the TOP 3 products" or "Find the BEST 5 customers"

### Task Example:
**"Find the top highest sales for each product"**

---

### Step-by-Step Solution

**Step 1: Get the basic data**
```sql
SELECT 
    order_id,
    product_id,
    sales
FROM sales_orders;
```

**Results:**
```
Order ID | Product ID | Sales
---------|------------|------
8        | 101        | 90
4        | 101        | 20
1        | 101        | 10
5        | 101        | 60
7        | 102        | 60
...
```

---

**Step 2: Add ranking within each product**
```sql
SELECT 
    order_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (
        PARTITION BY product_id 
        ORDER BY sales DESC
    ) AS rank_by_product
FROM sales_orders;
```

**Results:**
```
Order ID | Product ID | Sales | Rank by Product
---------|------------|-------|----------------
8        | 101        | 90    | 1  ← Highest for 101
5        | 101        | 60    | 2
4        | 101        | 20    | 3
1        | 101        | 10    | 4
---------|------------|-------|----------------
7        | 102        | 60    | 1  ← Highest for 102
6        | 102        | 40    | 2
3        | 102        | 15    | 3
---------|------------|-------|----------------
10       | 103        | 60    | 1  ← Highest for 103
9        | 103        | 20    | 2
```

**What happened?**
- Data divided by product_id (PARTITION BY)
- Within each product, sorted by sales (highest first)
- Each product gets its own ranking (1, 2, 3...)

---

**Step 3: Filter to get only rank 1 (the highest)**
```sql
SELECT * FROM (
    SELECT 
        order_id,
        product_id,
        sales,
        ROW_NUMBER() OVER (
            PARTITION BY product_id 
            ORDER BY sales DESC
        ) AS rank_by_product
    FROM sales_orders
) 
WHERE rank_by_product = 1;
```

**Final Results:**
```
Order ID | Product ID | Sales | Rank by Product
---------|------------|-------|----------------
8        | 101        | 90    | 1
7        | 102        | 60    | 1
10       | 103        | 60    | 1
```

**Success!** We found the highest sale for each product!

---

### Understanding the Query

**Why use subquery?**
We can't use WHERE with window functions directly, so we:
1. Create the ranking (inner query)
2. Filter the results (outer query)

**The pattern:**
```sql
SELECT * FROM (
    -- Inner query: Create ranking
    SELECT ..., ROW_NUMBER() OVER (...) AS rank
    FROM table
) 
WHERE rank = 1;  -- Outer query: Filter
```

---

### When to Use Top-N Analysis?

**Business situations:**
- "Show me the top 5 best-selling products"
- "Who are our top 10 customers?"
- "What are the 3 highest-rated items?"
- "Find the best performer in each department"

**Why it's important:**
- Focus on what works best
- Reward top performers
- Make smart business decisions
- Prioritize resources

---

## Use Case 2: Bottom-N Analysis

**What is it?** Finding the worst performers or lowest values

**Business Question:** "Show me the BOTTOM 2 customers" or "Find the WORST 5 products"

### Task Example:
**"Find the lowest two customers based on their total sales"**

**Why do businesses need this?**
- Cut costs
- Identify problems
- Stop selling unprofitable products
- Focus improvement efforts

---

### Step-by-Step Solution

**Step 1: Get customer sales data**
```sql
SELECT 
    order_id,
    customer_id,
    sales
FROM sales_orders;
```

**Results:**
```
Order ID | Customer ID | Sales
---------|-------------|------
1        | 3           | 10
2        | 2           | 20
3        | 4           | 15
4        | 3           | 20
...
```

Multiple rows per customer - we need TOTAL per customer!

---

**Step 2: Calculate total sales per customer (GROUP BY)**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales
FROM sales_orders
GROUP BY customer_id;
```

**Results:**
```
Customer ID | Total Sales
------------|------------
1           | 120
2           | 35
3           | 125
4           | 90
```

Now we have one row per customer with their total!

---

**Step 3: Add ranking from lowest to highest**
```sql
SELECT 
    customer_id,
    SUM(sales) AS total_sales,
    ROW_NUMBER() OVER (
        ORDER BY SUM(sales) ASC
    ) AS rank_customers
FROM sales_orders
GROUP BY customer_id;
```

**Results:**
```
Customer ID | Total Sales | Rank Customers
------------|-------------|---------------
2           | 35          | 1  ← Lowest
4           | 90          | 2
1           | 120         | 3
3           | 125         | 4  ← Highest
```

**Important:** We use `ORDER BY SUM(sales) ASC` (ascending) because we want lowest first!

---

**Step 4: Filter to get bottom 2**
```sql
SELECT * FROM (
    SELECT 
        customer_id,
        SUM(sales) AS total_sales,
        ROW_NUMBER() OVER (
            ORDER BY SUM(sales) ASC
        ) AS rank_customers
    FROM sales_orders
    GROUP BY customer_id
)
WHERE rank_customers <= 2;
```

**Final Results:**
```
Customer ID | Total Sales | Rank Customers
------------|-------------|---------------
2           | 35          | 1
4           | 90          | 2
```

**Success!** We found the 2 customers with lowest total sales!

---

### Important Rule: GROUP BY + Window Functions

**When using both together:**
Only use columns that appear in GROUP BY clause!

**Correct:**
```sql
SELECT 
    customer_id,           -- In GROUP BY ✓
    SUM(sales),            -- Aggregate function ✓
    ROW_NUMBER() OVER (ORDER BY SUM(sales))  -- Uses aggregate ✓
FROM sales_orders
GROUP BY customer_id;      -- Groups by customer_id
```

**Wrong:**
```sql
SELECT 
    customer_id,
    order_id,              -- NOT in GROUP BY ✗
    SUM(sales)
FROM sales_orders
GROUP BY customer_id;
```

---

### When to Use Bottom-N Analysis?

**Business situations:**
- "Which 5 products sell the least?"
- "Who are our 3 weakest salespeople?"
- "What are the lowest-rated items?"
- "Find underperforming departments"

**Why it's important:**
- Identify problems early
- Cut losses
- Improve weak areas
- Save money

---

## Top-N vs Bottom-N: The Difference

| Aspect | Top-N Analysis | Bottom-N Analysis |
|--------|----------------|-------------------|
| **Goal** | Find BEST | Find WORST |
| **Sort** | DESC (highest first) | ASC (lowest first) |
| **Business use** | Reward, focus on success | Fix, cut costs |
| **Example** | Top 5 products | Bottom 3 sellers |

**Same technique, different direction!**

---

## Complete Comparison Example

### Data: Customer Total Sales
```
Customer | Total Sales
---------|------------
A        | 150
B        | 80
C        | 200
D        | 80
E        | 120
```

### Top 2 (Highest):
```sql
ORDER BY total_sales DESC
```
**Result:** C (200), A (150)

### Bottom 2 (Lowest):
```sql
ORDER BY total_sales ASC
```
**Result:** B (80), D (80)

---

## The Subquery Pattern

**Template for Top/Bottom-N queries:**

```sql
SELECT * FROM (
    -- Inner query: Create ranking
    SELECT 
        columns,
        ROW_NUMBER() OVER (
            [PARTITION BY group_column]
            ORDER BY sort_column [DESC/ASC]
        ) AS rank_name
    FROM table
    [GROUP BY columns]
) 
WHERE rank_name <= N;  -- N = how many you want
```

**Replace:**
- `N` = how many you want (1, 2, 3, 5, 10...)
- `DESC` = for TOP-N (highest first)
- `ASC` = for BOTTOM-N (lowest first)

---

## Real-World Examples

### Example 1: School Rankings
**Top 3 students per class:**
```sql
SELECT * FROM (
    SELECT 
        student_name,
        class,
        marks,
        ROW_NUMBER() OVER (
            PARTITION BY class 
            ORDER BY marks DESC
        ) AS rank
    FROM students
) 
WHERE rank <= 3;
```

### Example 2: E-commerce
**Top 5 selling products:**
```sql
SELECT * FROM (
    SELECT 
        product_name,
        SUM(quantity) AS total_sold,
        ROW_NUMBER() OVER (
            ORDER BY SUM(quantity) DESC
        ) AS rank
    FROM orders
    GROUP BY product_name
) 
WHERE rank <= 5;
```

---

## Key Points to Remember

1. **ROW_NUMBER is most commonly used** in real projects
2. **Top-N Analysis** = Find the BEST (ORDER BY DESC)
3. **Bottom-N Analysis** = Find the WORST (ORDER BY ASC)
4. **Use subqueries** to filter by rank
5. **Can combine with GROUP BY** for aggregations
6. **PARTITION BY** to rank within groups
7. **Very important for business decisions!**

---

## Practice Questions

1. What's the difference between Top-N and Bottom-N analysis?
2. Why do we need a subquery to filter by rank?
3. What does ORDER BY DESC do vs ASC?
4. If you want top 5 products per category, what do you use?
5. When combining GROUP BY and window functions, what's the rule?

---

## Quick Decision Guide

**Need to find:**
- Best performers? → Top-N (ORDER BY DESC)
- Worst performers? → Bottom-N (ORDER BY ASC)
- Per category? → Add PARTITION BY
- With totals? → Use GROUP BY first

**Always use ROW_NUMBER for Top/Bottom-N analysis!**

---

## Use Case 3: Assigning Unique IDs

**What is it?** Creating a unique identifier for every row in a table

**Business Question:** "This table has no primary key - how do I give each row a unique ID?"

### The Problem: Tables Without Primary Keys

Sometimes you have a table where:
- No column has unique values
- There are duplicate IDs
- You need a way to identify each row uniquely

**Example Problem:**
```
Order ID | Order Date | Product
---------|------------|--------
4        | 2024-01-01 | Cap
6        | 2024-01-02 | Glove
4        | 2024-01-03 | Shoe  ← Duplicate ID!
6        | 2024-01-04 | Cap   ← Duplicate ID!
6        | 2024-01-05 | Shoe  ← Duplicate ID!
```

**Issue:** Order ID 4 appears twice, Order ID 6 appears three times!

---

### Task Example:
**"Assign unique IDs to the rows of the table orders_archive"**

---

### Step-by-Step Solution

**Step 1: Check the table**
```sql
SELECT * FROM sales_orders_archive;
```

**Results:**
```
Order ID | Order Date | Product | Sales
---------|------------|---------|------
4        | 2024-01-01 | Cap     | 50
6        | 2024-01-02 | Glove   | 30
4        | 2024-01-03 | Shoe    | 40  ← Duplicate!
6        | 2024-01-04 | Cap     | 20  ← Duplicate!
1        | 2024-01-05 | Glove   | 10
6        | 2024-01-06 | Shoe    | 60  ← Duplicate!
...
```

**Problem identified:** Order IDs repeat - not unique!

---

**Step 2: Generate unique IDs using ROW_NUMBER**
```sql
SELECT 
    ROW_NUMBER() OVER (
        ORDER BY order_id, order_date
    ) AS unique_id,
    order_id,
    order_date,
    product,
    sales
FROM sales_orders_archive;
```

**Results:**
```
Unique ID | Order ID | Order Date | Product | Sales
----------|----------|------------|---------|------
1         | 4        | 2024-01-01 | Cap     | 50
2         | 4        | 2024-01-03 | Shoe    | 40
3         | 6        | 2024-01-02 | Glove   | 30
4         | 6        | 2024-01-04 | Cap     | 20
5         | 6        | 2024-01-06 | Shoe    | 60
6         | 1        | 2024-01-05 | Glove   | 10
...
10        | ...      | ...        | ...     | ...
```

**Success!** Every row now has a unique ID (1, 2, 3, 4, 5... 10)

---

### Understanding the Query

**Why ORDER BY order_id, order_date?**
- Gives consistent ordering
- If you run query again, same IDs assigned
- Multiple columns ensure stable sorting

**Can use any columns:**
```sql
ROW_NUMBER() OVER (ORDER BY order_date)
ROW_NUMBER() OVER (ORDER BY order_id, product)
ROW_NUMBER() OVER (ORDER BY sales DESC)
```

**Key Point:** No PARTITION BY needed - we want ONE sequence for entire table!

---

### Why Are Unique IDs Important?

**1. Joining Tables**
Connect this table to other tables using the unique ID

```sql
SELECT * 
FROM orders_archive AS a
JOIN other_table AS b
ON a.unique_id = b.reference_id
```

---

**2. Importing/Exporting Data**
- Track which rows were imported
- Identify specific rows during data transfer
- Handle errors more easily

---

**3. Pagination**
Break large datasets into smaller pages

**Example:** Display 10 rows per page
- Page 1: IDs 1-10
- Page 2: IDs 11-20
- Page 3: IDs 21-30

```sql
-- Get page 2 (rows 11-20)
SELECT * FROM (
    SELECT 
        ROW_NUMBER() OVER (ORDER BY order_date) AS unique_id,
        order_id,
        product,
        sales
    FROM orders_archive
)
WHERE unique_id BETWEEN 11 AND 20;
```

---

**4. Performance Optimization**
- Faster queries when you have an indexed ID
- Better database performance
- Easier to reference specific rows

---

### What is Pagination?

**Pagination** = Dividing large data into smaller pages (like pages in a book!)

**Why use it?**
- Don't load 1 million rows at once
- Faster for users
- Less memory used
- Better performance

**Real-Life Example:** Google Search Results
- Page 1: Results 1-10
- Page 2: Results 11-20
- Page 3: Results 21-30

**SQL for Pagination:**
```sql
-- Page 1 (first 100 rows)
WHERE unique_id BETWEEN 1 AND 100

-- Page 2 (next 100 rows)
WHERE unique_id BETWEEN 101 AND 200

-- Page 3 (next 100 rows)
WHERE unique_id BETWEEN 201 AND 300
```

---

### Complete Pagination Example

**Scenario:** You have 10,000 orders, want to show 100 per page

**Step 1: Add unique IDs**
```sql
SELECT 
    ROW_NUMBER() OVER (ORDER BY order_date) AS unique_id,
    order_id,
    product,
    sales
FROM orders_archive;
```

**Step 2: Get specific page**
```sql
SELECT * FROM (
    SELECT 
        ROW_NUMBER() OVER (ORDER BY order_date) AS unique_id,
        order_id,
        product,
        sales
    FROM orders_archive
)
WHERE unique_id BETWEEN 101 AND 200;  -- Page 2
```

**Result:** Only rows 101-200 returned (100 rows)

---

### Formula for Pagination

**Page size** = How many rows per page
**Page number** = Which page you want

**Calculate range:**
```
Start = (page_number - 1) × page_size + 1
End = page_number × page_size
```

**Examples:**
- Page 1, size 100: Start=1, End=100
- Page 2, size 100: Start=101, End=200
- Page 5, size 50: Start=201, End=250

---

### When to Use Unique ID Generation?

**Use this technique when:**
- Table has no primary key
- Existing IDs have duplicates
- Need to track individual rows
- Preparing data for export/import
- Implementing pagination
- Optimizing database performance

---

### Important Notes

**1. Temporary vs Permanent IDs**
- ROW_NUMBER creates temporary IDs
- They're generated each time you run the query
- For permanent IDs, create a new column in the table

**2. Consistency Matters**
- Always use same ORDER BY
- Otherwise, same row might get different IDs each time

**3. No PARTITION BY**
- We want ONE continuous sequence
- PARTITION BY would restart numbering for each group

---

### Real-World Example: Student Registration

**Problem:** Student list without unique IDs
```
Name    | Class | Roll No
--------|-------|--------
Amit    | 7A    | 5
Priya   | 7B    | 5  ← Same roll no!
Raj     | 7A    | 5  ← Same roll no!
```

**Solution:** Generate unique student IDs
```sql
SELECT 
    ROW_NUMBER() OVER (ORDER BY class, name) AS student_id,
    name,
    class,
    roll_no
FROM students;
```

**Result:**
```
Student ID | Name  | Class | Roll No
-----------|-------|-------|--------
1          | Amit  | 7A    | 5
2          | Raj   | 7A    | 5
3          | Priya | 7B    | 5
```

Now each student has a unique ID!

---

### Key Points to Remember

1. **ROW_NUMBER generates unique IDs** for every row
2. **No PARTITION BY** when you want one continuous sequence
3. **ORDER BY is important** for consistent results
4. **Useful for tables without primary keys**
5. **Enables pagination** for large datasets
6. **Helps with joins, imports, exports**
7. **Improves database performance**

---

### Quick Template

```sql
-- Generate unique IDs
SELECT 
    ROW_NUMBER() OVER (
        ORDER BY column1, column2
    ) AS unique_id,
    other_columns
FROM your_table;

-- With pagination (get specific page)
SELECT * FROM (
    SELECT 
        ROW_NUMBER() OVER (ORDER BY column) AS unique_id,
        other_columns
    FROM your_table
)
WHERE unique_id BETWEEN start AND end;
```

Replace:
- `column1, column2` = columns to sort by
- `start, end` = page range you want

---

## Use Case 4: Identifying and Removing Duplicates

**What is it?** Finding duplicate rows and cleaning up data

**Business Question:** "We have duplicate records - which ones should we keep?"

### The Problem: Data Quality Issues

Sometimes databases have duplicate records:
- Same order appears multiple times
- Customer information duplicated
- Product entries repeated

**This is a DATA QUALITY ISSUE!**

---

### Task Example:
**"Identify duplicate rows in the table orders_archive and return a clean result without any duplicates"**

**Two goals:**
1. Identify which rows are duplicates
2. Return only clean data (no duplicates)

---

### Step-by-Step Solution

**Step 1: Check the data**
```sql
SELECT * FROM sales_orders_archive;
```

**Results:**
```
Order ID | Product | Status    | Creation Time
---------|---------|-----------|------------------
1        | Cap     | Delivered | 2024-01-01 10:00
2        | Glove   | Shipped   | 2024-01-02 11:00
4        | Shoe    | Pending   | 2024-01-03 09:00  ← Duplicate!
4        | Shoe    | Delivered | 2024-01-03 15:00  ← Duplicate!
6        | Cap     | Pending   | 2024-01-04 08:00  ← Duplicate!
6        | Cap     | Shipped   | 2024-01-04 12:00  ← Duplicate!
6        | Cap     | Delivered | 2024-01-04 16:00  ← Duplicate!
7        | Glove   | Delivered | 2024-01-05 10:00
```

**Problem:** Order 4 appears twice, Order 6 appears THREE times!

---

### Which Duplicate to Keep?

**Business Logic:** Keep the **most recent** record (latest creation time)

**Why?**
- Latest data is most up-to-date
- Shows final status of the order
- Contains most accurate information

**For Order 4:**
- First: 09:00 (older) ❌
- Second: 15:00 (newer) ✓ Keep this!

**For Order 6:**
- First: 08:00 (oldest) ❌
- Second: 12:00 (middle) ❌
- Third: 16:00 (newest) ✓ Keep this!

---

**Step 2: Rank records by creation time**
```sql
SELECT 
    order_id,
    product,
    status,
    creation_time,
    ROW_NUMBER() OVER (
        PARTITION BY order_id 
        ORDER BY creation_time DESC
    ) AS rn
FROM sales_orders_archive;
```

**Results:**
```
Order ID | Product | Status    | Creation Time      | RN
---------|---------|-----------|-------------------|----
1        | Cap     | Delivered | 2024-01-01 10:00  | 1  ✓
2        | Glove   | Shipped   | 2024-01-02 11:00  | 1  ✓
4        | Shoe    | Delivered | 2024-01-03 15:00  | 1  ✓ Latest!
4        | Shoe    | Pending   | 2024-01-03 09:00  | 2  ❌ Older
6        | Cap     | Delivered | 2024-01-04 16:00  | 1  ✓ Latest!
6        | Cap     | Shipped   | 2024-01-04 12:00  | 2  ❌ Older
6        | Cap     | Pending   | 2024-01-04 08:00  | 3  ❌ Oldest
7        | Glove   | Delivered | 2024-01-05 10:00  | 1  ✓
```

**Key observations:**
- **RN = 1**: The most recent record for each order ✓
- **RN = 2, 3**: Old duplicates that should be removed ❌

---

### Understanding the Query

**PARTITION BY order_id:**
- Creates separate groups for each order ID
- Each order gets its own ranking

**ORDER BY creation_time DESC:**
- Sorts by time (newest first)
- Most recent gets rank 1

**If RN = 1:** Only one record (no duplicates) OR newest record
**If RN > 1:** Duplicate records (old data)

---

**Step 3: Keep only the latest records (RN = 1)**
```sql
SELECT * FROM (
    SELECT 
        order_id,
        product,
        status,
        creation_time,
        ROW_NUMBER() OVER (
            PARTITION BY order_id 
            ORDER BY creation_time DESC
        ) AS rn
    FROM sales_orders_archive
)
WHERE rn = 1;
```

**Final Clean Results:**
```
Order ID | Product | Status    | Creation Time
---------|---------|-----------|------------------
1        | Cap     | Delivered | 2024-01-01 10:00
2        | Glove   | Shipped   | 2024-01-02 11:00
4        | Shoe    | Delivered | 2024-01-03 15:00
6        | Cap     | Delivered | 2024-01-04 16:00
7        | Glove   | Delivered | 2024-01-05 10:00
```

**Success!** All duplicates removed, only latest records kept!

---

### Finding Bad Data (Duplicates to Report)

**Step 4: Identify ONLY the duplicate records**
```sql
SELECT * FROM (
    SELECT 
        order_id,
        product,
        status,
        creation_time,
        ROW_NUMBER() OVER (
            PARTITION BY order_id 
            ORDER BY creation_time DESC
        ) AS rn
    FROM sales_orders_archive
)
WHERE rn > 1;
```

**Results (Bad Data):**
```
Order ID | Product | Status  | Creation Time      | RN
---------|---------|---------|-------------------|----
4        | Shoe    | Pending | 2024-01-03 09:00  | 2
6        | Cap     | Shipped | 2024-01-04 12:00  | 2
6        | Cap     | Pending | 2024-01-04 08:00  | 3
```

**Use this to:**
- Export list of problematic records
- Send to source system team
- Report data quality issues
- Track duplicate patterns

---

### Why This is Important

**1. Data Cleansing**
- Remove bad data before analysis
- Ensure accurate results
- Improve data quality

**2. Accurate Reporting**
- No double-counting
- Correct statistics
- Reliable insights

**3. Database Performance**
- Less storage used
- Faster queries
- Better efficiency

**4. Communication**
- Report issues to source systems
- Help teams fix root causes
- Improve data collection processes

---

### Real-World Example: Student Records

**Problem:** Student appears multiple times with different addresses

```
Student ID | Name  | Address   | Update Time
-----------|-------|-----------|------------------
101        | Amit  | Delhi     | 2024-01-01
101        | Amit  | Mumbai    | 2024-02-15  ← Latest!
101        | Amit  | Chennai   | 2024-01-20
```

**Solution:** Keep only the latest address
```sql
SELECT * FROM (
    SELECT 
        student_id,
        name,
        address,
        update_time,
        ROW_NUMBER() OVER (
            PARTITION BY student_id 
            ORDER BY update_time DESC
        ) AS rn
    FROM students
)
WHERE rn = 1;
```

**Result:** Amit's current address is Mumbai (most recent)

---

### Data Engineer vs Data Analyst

**Data Engineer:**
- Cleans and prepares data
- Removes duplicates
- Ensures data quality
- Sets up data pipelines

**Data Analyst:**
- Analyzes clean data
- Creates reports
- Finds insights
- Makes recommendations

**Important:** Data must be cleaned BEFORE analysis!

---

### Common Duplicate Scenarios

**Scenario 1: Multiple Updates**
- Record updated several times
- Keep latest version

**Scenario 2: System Errors**
- Same record inserted twice
- Keep one, remove duplicate

**Scenario 3: Data Migration**
- Old and new records mixed
- Keep most recent

**Scenario 4: User Errors**
- Form submitted multiple times
- Keep first or last submission

---

### Template for Duplicate Removal

```sql
-- Find and remove duplicates
SELECT * FROM (
    SELECT 
        *,  -- All columns
        ROW_NUMBER() OVER (
            PARTITION BY unique_identifier_columns
            ORDER BY timestamp_column DESC
        ) AS rn
    FROM table_name
)
WHERE rn = 1;  -- Keep only latest

-- OR find duplicates to report
WHERE rn > 1;  -- Show only duplicates
```

**Replace:**
- `unique_identifier_columns` = What makes it a duplicate (like order_id)
- `timestamp_column` = Time column to find latest
- `DESC` = For newest first (or ASC for oldest first)

---

### Key Decision: Which Record to Keep?

**Keep Latest (Most Common):**
```sql
ORDER BY creation_time DESC  -- Newest first
WHERE rn = 1
```

**Keep Oldest (Sometimes Needed):**
```sql
ORDER BY creation_time ASC  -- Oldest first
WHERE rn = 1
```

**Keep by Other Criteria:**
```sql
ORDER BY priority DESC, creation_time DESC
WHERE rn = 1
```

---

### Complete Example

**Task:** Clean customer orders, keep latest status

```sql
-- Step 1: View duplicates
SELECT 
    customer_id,
    order_id,
    status,
    order_date,
    ROW_NUMBER() OVER (
        PARTITION BY customer_id, order_id
        ORDER BY order_date DESC
    ) AS rn
FROM orders;

-- Step 2: Get clean data
SELECT * FROM (
    SELECT 
        customer_id,
        order_id,
        status,
        order_date,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id, order_id
            ORDER BY order_date DESC
        ) AS rn
    FROM orders
)
WHERE rn = 1;

-- Step 3: Export bad data for review
SELECT * FROM (
    SELECT 
        customer_id,
        order_id,
        status,
        order_date,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id, order_id
            ORDER BY order_date DESC
        ) AS rn
    FROM orders
)
WHERE rn > 1;
```

---

### Key Points to Remember

1. **ROW_NUMBER identifies duplicates** using PARTITION BY
2. **RN = 1** means latest (or only) record
3. **RN > 1** means duplicate/old records
4. **ORDER BY DESC** keeps newest record
5. **ORDER BY ASC** keeps oldest record
6. **Essential for data quality**
7. **Used by data engineers constantly**

---

## Summary: All Four Use Cases

### 1. Top-N Analysis
Find the BEST performers (highest sales, top students)

### 2. Bottom-N Analysis  
Find the WORST performers (lowest sales, underperformers)

### 3. Unique ID Generation
Create IDs for tables, enable pagination

### 4. Duplicate Detection & Removal
Clean data, ensure quality, remove duplicates

---

## Why ROW_NUMBER is So Popular

According to real project experience:

**ROW_NUMBER is the MOST USED ranking function because:**
- Simple and predictable
- Always unique numbers
- Solves many practical problems
- Essential for data cleaning
- Works great for Top/Bottom-N
- Perfect for pagination
- Easy to understand and use

**You'll use it A LOT in real projects!**

---

## Final Takeaway

**ROW_NUMBER is like a Swiss Army knife for data analysis:**
- Find best/worst performers
- Create unique identifiers
- Paginate large datasets
- Clean duplicate data
- Ensure data quality

**Master ROW_NUMBER and you'll solve 80% of ranking problems!**
