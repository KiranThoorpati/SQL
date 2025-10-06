# Understanding SQL CAST Function - Complete Guide

## What is CAST?

**CAST** is a simple SQL function that **changes data from one type to another**. That's it - just one job! No formatting, just converting.

Think of it like transforming water into ice - same thing, different form! ❄️

## Basic Syntax

The CAST syntax is unique and reads like English:

```sql
CAST(value AS data_type)
```

**Notice**: It uses the word **AS** instead of a comma - very natural!

**Example in English**: "Cast this string as an integer"

---

## Example 1: String to Integer

```sql
SELECT 
    '123' AS Original_String,
    CAST('123' AS INT) AS [String to Integer];
```

**Output:**
```
Original_String | String to Integer
----------------|------------------
123             | 123
```

The first is text (you can't do math), the second is a number (you can do math)!

---

## Example 2: Integer to String

```sql
SELECT 
    123 AS Original_Integer,
    CAST(123 AS VARCHAR) AS [Int to String];
```

**Output:**
```
Original_Integer | Int to String
-----------------|---------------
123              | 123
```

Now it's text, not a number!

---

## Example 3: String to Date

```sql
SELECT 
    '2025-08-20' AS Original_String,
    CAST('2025-08-20' AS DATE) AS [String to Date];
```

**Output:**
```
Original_String | String to Date
----------------|----------------
2025-08-20      | 2025-08-20
```

Now SQL recognizes it as a date!

---

## Example 4: String to DateTime

```sql
SELECT 
    '2025-08-20' AS Original_String,
    CAST('2025-08-20' AS DATETIME) AS [String to DateTime];
```

**Output:**
```
Original_String | String to DateTime
----------------|--------------------
2025-08-20      | 2025-08-20 00:00:00
```

Since we didn't provide time, SQL adds 00:00:00 (midnight)!

---

## Example 5: DateTime to Date (Remove Time)

This is super useful when you want to remove time information!

```sql
SELECT 
    OrderID,
    CreationTime,
    CAST(CreationTime AS DATE) AS [DateTime to Date]
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05 10:30:45' AS DATETIME) AS CreationTime
    UNION ALL
    SELECT 2, CAST('2025-02-15 14:20:30' AS DATETIME)
    UNION ALL
    SELECT 3, CAST('2025-03-22 09:15:55' AS DATETIME)
) AS Orders;
```

**Output:**
```
OrderID | CreationTime        | DateTime to Date
--------|---------------------|------------------
1       | 2025-01-05 10:30:45 | 2025-01-05
2       | 2025-02-15 14:20:30 | 2025-02-15
3       | 2025-03-22 09:15:55 | 2025-03-22
```

Time is gone - clean date only!

---

## Common Data Type Conversions

### Numbers

```sql
SELECT 
    CAST('123' AS INT) AS String_to_Int,
    CAST('123.45' AS DECIMAL(10,2)) AS String_to_Decimal,
    CAST(123 AS VARCHAR) AS Int_to_String,
    CAST(123.45 AS INT) AS Decimal_to_Int;
```

**Output:**
```
String_to_Int | String_to_Decimal | Int_to_String | Decimal_to_Int
--------------|-------------------|---------------|----------------
123           | 123.45            | 123           | 123
```

Note: When converting decimal to int, it rounds down!

---

### Dates

```sql
SELECT 
    CAST('2025-08-20' AS DATE) AS String_to_Date,
    CAST('2025-08-20 14:35:55' AS DATETIME) AS String_to_DateTime,
    CAST(GETDATE() AS DATE) AS DateTime_to_Date,
    CAST(GETDATE() AS VARCHAR) AS Date_to_String;
```

**Sample Output:**
```
String_to_Date | String_to_DateTime  | DateTime_to_Date | Date_to_String
---------------|---------------------|------------------|------------------------
2025-08-20     | 2025-08-20 14:35:55 | 2025-10-06       | Oct  6 2025 12:00AM
```

---

## Real-World Example: Clean Sales Report

```sql
SELECT 
    OrderID,
    OrderDate,
    OrderAmount,
    
    -- Remove time from dates
    CAST(OrderDate AS DATE) AS Clean_OrderDate,
    
    -- Convert amount to string for display
    '$' + CAST(OrderAmount AS VARCHAR) AS Amount_Text,
    
    -- Extract just the year
    CAST(YEAR(OrderDate) AS VARCHAR) AS Order_Year
    
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15 10:30:00' AS DATETIME) AS OrderDate, 1234.50 AS OrderAmount
    UNION ALL
    SELECT 2, CAST('2025-02-20 14:45:00' AS DATETIME), 5678.99
    UNION ALL
    SELECT 3, CAST('2025-03-10 09:15:00' AS DATETIME), 999.00
) AS Orders;
```

**Output:**
```
OrderID | OrderDate           | OrderAmount | Clean_OrderDate | Amount_Text | Order_Year
--------|---------------------|-------------|-----------------|-------------|------------
1       | 2025-01-15 10:30:00 | 1234.50     | 2025-01-15      | $1234.50    | 2025
2       | 2025-02-20 14:45:00 | 5678.99     | 2025-02-20      | $5678.99    | 2025
3       | 2025-03-10 09:15:00 | 999.00      | 2025-03-10      | $999.00     | 2025
```

---

## Understanding: CAST Only Converts, Doesn't Format!

**Important**: CAST always gives you the **standard SQL format** - you can't customize it!

```sql
SELECT 
    CAST('2025-08-20' AS DATE) AS Using_CAST;
```

**Output:**
```
Using_CAST
----------
2025-08-20
```

You always get: YYYY-MM-DD format (SQL's standard)

**Want custom format?** Use CONVERT or FORMAT instead!

---

## Complete Comparison: CAST vs CONVERT vs FORMAT

### Feature Comparison Table

| Feature | CAST | CONVERT | FORMAT |
|---------|------|---------|--------|
| **Changes Data Type** | ✅ Yes | ✅ Yes | ✅ Yes (to string only) |
| **Formats Dates** | ❌ No | ✅ Yes | ✅ Yes |
| **Formats Numbers** | ❌ No | ❌ No | ✅ Yes |
| **Custom Formats** | ❌ No | ⚠️ Limited (style numbers) | ✅ Yes (full control) |
| **Syntax** | Natural (AS) | Standard (commas) | Standard (commas) |
| **Speed** | ⚡ Fastest | ⚡ Fast | 🐌 Slower |

---

## When to Use Each Function?

### Use CAST when:
✅ You only need to change data type  
✅ You want simple, clean code  
✅ You don't need any formatting  
✅ You want fast performance

**Example:**
```sql
CAST('123' AS INT)
CAST(OrderDate AS DATE)
CAST(Price AS VARCHAR)
```

### Use CONVERT when:
✅ You need to change type AND format  
✅ You're working with dates  
✅ You want to use predefined styles  
✅ You're in SQL Server

**Example:**
```sql
CONVERT(VARCHAR, OrderDate, 101)  -- USA format
```

### Use FORMAT when:
✅ You need custom formats  
✅ You want full control  
✅ You're formatting numbers as currency/percentage  
✅ Readability is more important than speed

**Example:**
```sql
FORMAT(OrderDate, 'MMMM dd, yyyy')
FORMAT(Price, 'C')
```

---

## Side-by-Side Comparison

```sql
SELECT 
    CAST('2025-08-20' AS DATE) AS OriginalDate,
    
    -- CAST: Just converts (always standard format)
    CAST(CAST('2025-08-20' AS DATE) AS VARCHAR) AS Using_CAST,
    
    -- CONVERT: Converts + formats with style number
    CONVERT(VARCHAR, CAST('2025-08-20' AS DATE), 101) AS Using_CONVERT,
    
    -- FORMAT: Converts + custom format
    FORMAT(CAST('2025-08-20' AS DATE), 'MM/dd/yyyy') AS Using_FORMAT;
```

**Output:**
```
OriginalDate | Using_CAST | Using_CONVERT | Using_FORMAT
-------------|------------|---------------|-------------
2025-08-20   | 2025-08-20 | 08/20/2025    | 08/20/2025
```

---

## Common CAST Use Cases

### 1. Clean Data Before Calculations

```sql
SELECT 
    CAST('100' AS INT) + CAST('200' AS INT) AS Total;
```

**Output:**
```
Total
-----
300
```

Without CAST, SQL would try to add text, which doesn't work!

---

### 2. Remove Time for Date Comparisons

```sql
SELECT 
    OrderID,
    CreationTime
FROM Orders
WHERE CAST(CreationTime AS DATE) = '2025-01-15';
```

This finds all orders from January 15, regardless of time!

---

### 3. Prepare Data for Export

```sql
SELECT 
    OrderID,
    CAST(OrderDate AS VARCHAR) AS OrderDate_Text,
    CAST(OrderAmount AS VARCHAR) AS Amount_Text
FROM Orders;
```

Everything is now text - ready for CSV export!

---

## Important Notes

### ⚠️ CAST Cannot Format!

```sql
-- This gives standard format only
CAST(OrderDate AS VARCHAR)

-- Want USA format? Use CONVERT instead
CONVERT(VARCHAR, OrderDate, 101)

-- Want custom format? Use FORMAT instead
FORMAT(OrderDate, 'MM/dd/yyyy')
```

### ⚠️ Be Careful with Data Loss

```sql
SELECT CAST(123.89 AS INT);  -- Result: 123 (decimals lost!)
```

---

## Practice Exercise

Try this yourself:

```sql
-- 1. Convert string to number and do math
SELECT CAST('50' AS INT) + CAST('25' AS INT) AS Sum;

-- 2. Remove time from current date
SELECT CAST(GETDATE() AS DATE) AS Today;

-- 3. Convert number to text
SELECT 'Order #' + CAST(123 AS VARCHAR) AS OrderNumber;

-- 4. Clean datetime column
SELECT 
    OrderID,
    CAST(CreationTime AS DATE) AS Order_Date
FROM Orders;
```

---

## Quick Reference

### Common CAST Patterns

```sql
-- String to Number
CAST('123' AS INT)
CAST('123.45' AS DECIMAL(10,2))

-- Number to String
CAST(123 AS VARCHAR)
CAST(123.45 AS VARCHAR)

-- String to Date
CAST('2025-08-20' AS DATE)
CAST('2025-08-20 14:35:55' AS DATETIME)

-- Date Conversions
CAST(GETDATE() AS DATE)           -- Remove time
CAST(OrderDate AS VARCHAR)        -- Date to text
```

---

## Key Points to Remember

1. **CAST only converts** - no formatting options
2. **Syntax is natural**: `CAST(value AS type)`
3. **Always gives standard format** from SQL
4. **Fastest conversion function** available
5. **Works in all SQL databases** (most portable)
6. **Perfect for simple type changes** without formatting needs
7. **Can lose data** (like decimals when converting to INT)

---

## Summary

**CAST = Simple Type Conversion**
- One job: Change data type
- Fast and efficient
- Clean, readable syntax
- No formatting options
- Use when you just need to convert!

Now you're a CAST expert!
