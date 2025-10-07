# SQL RIGHT ANTI JOIN - Easy Guide for Beginners

## What is a RIGHT ANTI JOIN?

A **RIGHT ANTI JOIN** is the **opposite of LEFT ANTI JOIN**. It returns only the rows from the right table that have **NO MATCH** in the left table. It helps you find orphaned or invalid records!

Think of it like this:
- **Left Table** = Used only for checking (not for getting data)
- **Right Table** = Show me ONLY the unmatched rows (things that exist here but not there)

<img width="1915" height="1079" alt="image" src="https://github.com/user-attachments/assets/5456522c-e062-459d-b900-344ee875e25c" />

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/98450e18-58e0-4409-aa87-968ce1965c78" />

## Visual Example

```
Table A (Left)          Table B (Right)
    ●●●                     ●●●●●●●
 USED FOR                ONLY THE PART
 CHECKING ONLY           WITH NO MATCH
```

## Basic Syntax

```sql
SELECT columns
FROM left_table A
RIGHT JOIN right_table B
ON A.key = B.key
WHERE A.key IS NULL;
```

⚠️ **Important:** 
- Use RIGHT JOIN first
- Then add WHERE to filter out matches
- Check if the left table's key IS NULL

---

## Real Example: Finding Orders Without Valid Customers

### Our Tables

**Customers Table:**
```
CustomerID | FirstName
-----------|----------
1          | John
2          | Sarah
3          | Mike
```

**Orders Table:**
```
OrderID | CustomerID | Sales
--------|------------|-------
101     | 1          | 500
102     | 2          | 300
103     | 3          | 450
104     | 99         | 200
```

*Notice: Order 104 has CustomerID 99, but that customer doesn't exist!*

### Task
Get all orders **without matching customers** (invalid/orphaned orders).

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/51a59f1c-a94f-4f97-8478-91c64a9cef45" />

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/9d0c9e1e-83a9-41a1-9a93-b9a1fce66cab" />

---

### Step 1: Start with RIGHT JOIN

```sql
SELECT 
    Customers.CustomerID,
    Customers.FirstName,
    Orders.OrderID,
    Orders.CustomerID AS OrderCustomerID,
    Orders.Sales
FROM Customers
RIGHT JOIN Orders
ON Customers.CustomerID = Orders.CustomerID;
```

**Output:**
```
CustomerID | FirstName | OrderID | OrderCustomerID | Sales
-----------|-----------|---------|-----------------|-------
1          | John      | 101     | 1               | 500
2          | Sarah     | 102     | 2               | 300
3          | Mike      | 103     | 3               | 450
NULL       | NULL      | 104     | 99              | 200
```

We can see all orders, and Order 104 has NULL for customer info!

---

### Step 2: Filter to Show Only Invalid Orders (✅ RIGHT ANTI JOIN!)

```sql
SELECT 
    Orders.OrderID,
    Orders.CustomerID,
    Orders.Sales
FROM Customers
RIGHT JOIN Orders
ON Customers.CustomerID = Orders.CustomerID
WHERE Customers.CustomerID IS NULL;
```

**Output:**
```
OrderID | CustomerID | Sales
--------|------------|-------
104     | 99         | 200
```

**Perfect!** Only the order with no valid customer! ✅

This is a **data quality issue** - someone needs to fix this order or find the missing customer!

---

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/c9b525f8-bf71-45aa-8064-680ed765aeae" />

<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/64a6d999-a132-47d3-aa56-732a58506b1a" />


## Better Alternative: Use LEFT JOIN Instead! ⭐

Just like with RIGHT JOIN, most programmers prefer LEFT JOIN. Just switch the table order!

```sql
SELECT 
    Orders.OrderID,
    Orders.CustomerID,
    Orders.Sales
FROM Orders
LEFT JOIN Customers
ON Orders.CustomerID = Customers.CustomerID
WHERE Customers.CustomerID IS NULL;
```

**Output:**
```
OrderID | CustomerID | Sales
--------|------------|-------
104     | 99         | 200
```

**Same result!** Start FROM the table you want to check (Orders), then use LEFT JOIN! ✅

---

## How It Works (Step-by-Step)

### Using RIGHT JOIN Method:

**Step 1:** RIGHT JOIN combines tables
- Order 101 → Found John ✓
- Order 102 → Found Sarah ✓
- Order 103 → Found Mike ✓
- Order 104 → No customer found (NULL)

**Step 2:** WHERE filters out valid orders
- The `WHERE Customers.CustomerID IS NULL` keeps only rows with no matching customer
- This removes Orders 101, 102, 103
- Keeps only Order 104

**Result:** Only orphaned orders!

---

## Another Example: Library Books Without Borrowers

### Tables

**Students:**
```
StudentID | Name
----------|--------
1         | Amy
2         | Bob
```

**Borrowed Books:**
```
BookID | StudentID | BookName
-------|-----------|-------------
201    | 1         | Harry Potter
202    | 2         | Math Guide
203    | 99        | Science Book
204    | 88        | History Book
```

### Task: Find books borrowed by students who don't exist in our system

```sql
SELECT 
    Books.BookID,
    Books.BookName,
    Books.StudentID
FROM Students
RIGHT JOIN Books
ON Students.StudentID = Books.StudentID
WHERE Students.StudentID IS NULL;
```

**Output:**
```
BookID | BookName      | StudentID
-------|---------------|----------
203    | Science Book  | 99
204    | History Book  | 88
```

**Problem found!** These books are checked out to invalid student IDs! Someone needs to investigate! 🔍

---

## The Two-Step Process

**Step 1:** Join the tables (use RIGHT JOIN)
```sql
RIGHT JOIN table_B ON condition
```
This shows everything from right table with NULLs where there's no match.

**Step 2:** Filter for NULLs (use WHERE)
```sql
WHERE table_A.key IS NULL
```
This keeps only the rows with no match!

---

## Key Points to Remember

✅ RIGHT ANTI JOIN finds records in the right table with no match in left table  
✅ Always use RIGHT JOIN first, then add WHERE  
✅ Check for `IS NULL` on the LEFT table's key column  
✅ Perfect for finding data quality issues or orphaned records  
✅ The left table is only used for checking, not for getting data  
✅ **Better to use LEFT JOIN by switching table order!**  

---

## Real-World Uses

- Find orders without valid customers (data cleaning!)
- Find transactions without valid accounts (fraud detection!)
- Find comments without valid users (clean up database!)
- Find assignments submitted by non-existent students (system error!)

---

## Practice Question

An online store has:
- **Products table**: 50 products
- **Sales table**: 200 sales records

**Question:** Write a query to find sales that reference products that **no longer exist** in the Products table (maybe they were deleted).

**Answer (Method 1 - RIGHT JOIN):**
```sql
SELECT Sales.SaleID, Sales.ProductID, Sales.Amount
FROM Products
RIGHT JOIN Sales
ON Products.ProductID = Sales.ProductID
WHERE Products.ProductID IS NULL;
```

**Answer (Method 2 - LEFT JOIN, Recommended!):**
```sql
SELECT Sales.SaleID, Sales.ProductID, Sales.Amount
FROM Sales
LEFT JOIN Products
ON Sales.ProductID = Products.ProductID
WHERE Products.ProductID IS NULL;
```

Both find broken references! 🔧

---

## Comparison: LEFT vs RIGHT ANTI JOIN

| Anti JOIN Type | What You Find |
|----------------|---------------|
| **LEFT ANTI JOIN** | Records in LEFT table with no match in RIGHT |
| **RIGHT ANTI JOIN** | Records in RIGHT table with no match in LEFT |

---

## The Two Ways to Do It

```sql
-- Method 1: Using RIGHT JOIN
FROM A RIGHT JOIN B WHERE A.key IS NULL

-- Method 2: Using LEFT JOIN (Better!)
FROM B LEFT JOIN A WHERE A.key IS NULL
```

**Both give identical results!** Most developers prefer Method 2. 👍

---

## 🎯 Remember the Formula!

```
RIGHT JOIN + WHERE left_key IS NULL = RIGHT ANTI JOIN
```

Or better:

```
Flip tables + LEFT JOIN + WHERE key IS NULL = Same thing!
```

This helps you find **invalid or orphaned records** in your database! 🔍
