# SQL Notes: IS NULL and IS NOT NULL Operators

## What Are These Operators?

These are special **checkers** that help us find missing data (NULLs) in our database.

### IS NULL
Checks if a value is missing (NULL)
- If NULL → Returns **TRUE** ✓
- If NOT NULL → Returns **FALSE** ✗

### IS NOT NULL
Checks if a value exists (NOT NULL)
- If NOT NULL → Returns **TRUE** ✓
- If NULL → Returns **FALSE** ✗

**Think of it like:** A detective searching for empty boxes! 🔍

---

## Syntax (How to Write It)

```sql
-- Check if value is NULL
WHERE ColumnName IS NULL

-- Check if value is NOT NULL
WHERE ColumnName IS NOT NULL
```

### Examples:
```sql
-- Find missing shipping addresses
WHERE ShippingAddress IS NULL

-- Find customers who HAVE addresses
WHERE ShippingAddress IS NOT NULL
```

---

## How It Works - Simple Example

Let's check if prices are NULL:

**Original Data:**
| Order | Price |
|-------|-------|
| 4     | 50    |
| 2     | NULL  |

### Using IS NULL:
```sql
Price IS NULL
```

**Results:**
| Order | Price | Price IS NULL |
|-------|-------|---------------|
| 4     | 50    | FALSE         |
| 2     | NULL  | TRUE          |

### Using IS NOT NULL:
```sql
Price IS NOT NULL
```

**Results:**
| Order | Price | Price IS NOT NULL |
|-------|-------|-------------------|
| 4     | 50    | TRUE              |
| 2     | NULL  | FALSE             |

**Important:** These operators return TRUE or FALSE, not the actual values!

---

## Use Case 1: Finding Missing Information

### Task: Find all customers who have NO scores

**Data:**
| CustomerID | Name  | Score |
|------------|-------|-------|
| 1          | John  | 85    |
| 2          | Sarah | 92    |
| 3          | Mike  | 78    |
| 4          | Lisa  | 88    |
| 5          | Anna  | NULL  |

**SQL Code:**
```sql
SELECT *
FROM Sales.Customers
WHERE Score IS NULL
```

**Result:**
| CustomerID | Name | Score |
|------------|------|-------|
| 5          | Anna | NULL  |

**What happened?** We found the ONE customer (Anna) who doesn't have a score!

---

## Use Case 2: Getting Clean Data (Removing NULLs)

### Task: Show all customers who HAVE scores

**SQL Code:**
```sql
SELECT *
FROM Sales.Customers
WHERE Score IS NOT NULL
```

**Result:**
| CustomerID | Name  | Score |
|------------|-------|-------|
| 1          | John  | 85    |
| 2          | Sarah | 92    |
| 3          | Mike  | 78    |
| 4          | Lisa  | 88    |

**What happened?** We removed Anna's row because her score is NULL. Now we have clean data for analysis!

---

## Advanced Use Case: Anti-Joins (Finding What's Missing)

### Quick Recap: Basic SQL Joins

**1. INNER JOIN** - Only matching rows
```
Left Table ∩ Right Table
```

**2. LEFT JOIN** - All left rows + matching right rows
```
All of Left + Matching from Right
```

**3. RIGHT JOIN** - All right rows + matching left rows
```
All of Right + Matching from Left
```

**4. FULL JOIN** - Everything from both tables
```
All of Left + All of Right
```

---

### New Advanced Joins!

**5. LEFT ANTI-JOIN** 🆕
- All rows from LEFT table
- WITHOUT any matching rows from RIGHT table
- **How:** LEFT JOIN + IS NULL

**6. RIGHT ANTI-JOIN** 🆕
- All rows from RIGHT table
- WITHOUT any matching rows from LEFT table
- **How:** RIGHT JOIN + IS NULL

---

## Visual Explanation: Anti-Join

### LEFT ANTI-JOIN

**Think of it like:** "Show me everything from the left that has NO partner on the right"

```
Left Table          Right Table
   ┌─────┐            ┌─────┐
   │  A  │────────────│  X  │  ← Has a match (skip it)
   ├─────┤            ├─────┤
   │  B  │  (no line) │  Y  │  ← B has NO match (KEEP IT!)
   ├─────┤            └─────┘
   │  C  │────────────
   └─────┘
```

**Result:** Only B appears (because it has no match)

---

## Real Example: Customers Without Orders

### Task: Find all customers who have NEVER placed an order

### Step 1: Start with Customers (LEFT table)
```sql
SELECT *
FROM Sales.Customers C
```

**Customers Table:**
| CustomerID | Name  |
|------------|-------|
| 1          | John  |
| 2          | Sarah |
| 3          | Mike  |
| 4          | Lisa  |
| 5          | Anna  |

---

### Step 2: Add LEFT JOIN with Orders (RIGHT table)
```sql
SELECT 
    C.*,
    O.OrderID
FROM Sales.Customers C
LEFT JOIN Sales.Orders O
    ON C.CustomerID = O.CustomerID
```

**Result After LEFT JOIN:**
| CustomerID | Name  | OrderID |
|------------|-------|---------|
| 1          | John  | 101     | ← Has orders
| 1          | John  | 102     | ← Has orders
| 2          | Sarah | 201     | ← Has orders
| 3          | Mike  | 301     | ← Has orders
| 4          | Lisa  | 401     | ← Has orders
| 5          | Anna  | NULL    | ← NO orders!

**Key Observation:** Anna has NULL for OrderID = She never ordered anything!

---

### Step 3: Filter for NULLs (Create Anti-Join Effect!)
```sql
SELECT 
    C.*,
    O.OrderID
FROM Sales.Customers C
LEFT JOIN Sales.Orders O
    ON C.CustomerID = O.CustomerID
WHERE O.CustomerID IS NULL
```

**Final Result:**
| CustomerID | Name | OrderID |
|------------|------|---------|
| 5          | Anna | NULL    |

**Success!** We found the customer who never placed an order!

---

## Why Use LEFT JOIN + IS NULL?

### Without IS NULL (Regular LEFT JOIN):
- Shows ALL customers
- Customers WITH orders show order details
- Customers WITHOUT orders show NULL

### With IS NULL (LEFT ANTI-JOIN):
- Shows ONLY customers WITHOUT orders
- Filters out everyone who has matches
- Perfect for finding "missing connections"

---

## Step-by-Step: How Anti-Join Works

**Step 1:** LEFT JOIN connects customers to orders
```
Customer 1 ──┐
Customer 2 ──┼──→ Orders exist
Customer 3 ──┤
Customer 4 ──┘
Customer 5 ──✗ (No connection = NULL)
```

**Step 2:** WHERE IS NULL filters
```
Customer 1 ─X─ (Has orders, filtered out)
Customer 2 ─X─ (Has orders, filtered out)
Customer 3 ─X─ (Has orders, filtered out)
Customer 4 ─X─ (Has orders, filtered out)
Customer 5 ─✓─ (No orders, KEPT!)
```

**Step 3:** Result shows only unmatched rows
```
Only Customer 5 appears!
```

---

## Real-World Examples

### Example 1: School Attendance
**Find students who didn't submit homework:**
```sql
SELECT S.*
FROM Students S
LEFT JOIN Homework H
    ON S.StudentID = H.StudentID
WHERE H.StudentID IS NULL
```

### Example 2: Library Books
**Find books that were never borrowed:**
```sql
SELECT B.*
FROM Books B
LEFT JOIN Loans L
    ON B.BookID = L.BookID
WHERE L.BookID IS NULL
```

### Example 3: Restaurant Orders
**Find menu items nobody ordered today:**
```sql
SELECT M.*
FROM MenuItems M
LEFT JOIN TodayOrders T
    ON M.ItemID = T.ItemID
WHERE T.ItemID IS NULL
```

---

## Comparison: Different Methods

### Method 1: IS NULL ✓ (Recommended)
```sql
WHERE O.CustomerID IS NULL
```
- Clear and easy to read
- Standard SQL practice

### Method 2: Using Order ID ✓ (Also works)
```sql
WHERE O.OrderID IS NULL
```
- Works the same way
- Better to use the join key

### ❌ DON'T Use Equal Sign!
```sql
WHERE O.CustomerID = NULL  -- WRONG!
```
This will **NOT** work! Always use **IS NULL**, never = NULL

---

## Key Takeaways

🎯 **IS NULL checks if something is missing**
- Returns TRUE or FALSE (not the actual value)

🎯 **IS NOT NULL checks if something exists**
- Use it to filter out NULLs and get clean data

🎯 **LEFT ANTI-JOIN = LEFT JOIN + IS NULL**
- Finds rows from the left table with NO match on the right
- Perfect for finding "what's missing"

🎯 **Common Uses:**
1. Find missing information
2. Clean up data by removing NULLs
3. Find records that don't have relationships (Anti-Join)

🎯 **Important Rules:**
- Use IS NULL, never = NULL
- Always use the join key column for anti-joins
- Anti-joins help find "orphan" records

---

## Memory Tricks 🧠

**"IS NULL = Empty Box Detector"**
Finds all the empty boxes in your data!

**"LEFT Anti-JOIN = Left Alone"**
Find all the left-side items that are "left alone" with no partner!

---

## Practice Questions

**Question 1:** What does `WHERE Price IS NULL` return?
**Answer:** All rows where the Price is missing (NULL)

**Question 2:** How do you find customers who HAVE email addresses?
**Answer:** `WHERE Email IS NOT NULL`

**Question 3:** What's the difference between LEFT JOIN and LEFT ANTI-JOIN?
**Answer:** 
- LEFT JOIN shows all left rows (with or without matches)
- LEFT ANTI-JOIN shows only left rows WITHOUT matches (using IS NULL filter)

**Question 4:** Why use `WHERE O.OrderID IS NULL` instead of `WHERE O.OrderID = NULL`?
**Answer:** SQL cannot compare using = with NULL. You MUST use IS NULL!
