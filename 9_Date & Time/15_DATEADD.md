# Understanding SQL DATEADD Function - Complete Guide

## What is DATEADD?

**DATEADD** is a SQL function that lets you **add or subtract time** from a date. You can add/subtract years, months, days, hours, minutes, and more!

Think of it like a time machine - you can move dates forward or backward in time!

## Basic Syntax

```sql
DATEADD(part, interval, date)
```

**Parameters:**
1. **part**: What you want to add (YEAR, MONTH, DAY, HOUR, etc.)
2. **interval**: How many to add (positive = add, negative = subtract)
3. **date**: The date you want to change

---

## How DATEADD Works - Visual Examples

Starting Date: **August 20, 2025**

### Adding Years
```
Add 3 years → August 20, 2028
```

### Adding Months
```
Add 2 months → October 20, 2025
```

### Adding Days
```
Add 5 days → August 25, 2025
```

### Subtracting Years
```
Subtract 3 years → August 20, 2022
```

### Subtracting Months
```
Subtract 2 months → June 20, 2025
```

### Subtracting Days
```
Subtract 5 days → August 15, 2025
```

---

## Example 1: Add Years to a Date

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    DATEADD(YEAR, 2, '2025-08-20') AS TwoYearsLater;
```

**Output:**
```
OriginalDate | TwoYearsLater
-------------|---------------
2025-08-20   | 2027-08-20
```

Added 2 years!

---

## Example 2: Add Months to a Date

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    DATEADD(MONTH, 3, '2025-08-20') AS ThreeMonthsLater;
```

**Output:**
```
OriginalDate | ThreeMonthsLater
-------------|------------------
2025-08-20   | 2025-11-20
```

Added 3 months (August → November)!

---

## Example 3: Add Days to a Date

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    DATEADD(DAY, 10, '2025-08-20') AS TenDaysLater;
```

**Output:**
```
OriginalDate | TenDaysLater
-------------|---------------
2025-08-20   | 2025-08-30
```

Added 10 days!

---

## Example 4: Subtract Years (Using Negative Numbers)

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    DATEADD(YEAR, -3, '2025-08-20') AS ThreeYearsEarlier;
```

**Output:**
```
OriginalDate | ThreeYearsEarlier
-------------|-------------------
2025-08-20   | 2022-08-20
```

Subtracted 3 years using **-3**!

---

## Example 5: Subtract Months

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    DATEADD(MONTH, -2, '2025-08-20') AS TwoMonthsEarlier;
```

**Output:**
```
OriginalDate | TwoMonthsEarlier
-------------|------------------
2025-08-20   | 2025-06-20
```

Subtracted 2 months (August → June)!

---

## Example 6: Subtract Days

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    DATEADD(DAY, -10, '2025-08-20') AS TenDaysEarlier;
```

**Output:**
```
OriginalDate | TenDaysEarlier
-------------|----------------
2025-08-20   | 2025-08-10
```

Subtracted 10 days!

---

## Working with a Table - Real Examples

Let's use a **SalesOrders** table:

```sql
OrderID | OrderDate
--------|------------
1       | 2025-01-05
2       | 2025-01-15
3       | 2025-01-22
4       | 2025-01-28
5       | 2025-02-03
6       | 2025-02-10
7       | 2025-02-15
8       | 2025-02-25
9       | 2025-03-07
10      | 2025-03-20
```

---

### Example 7: Add 2 Years to All Orders

```sql
SELECT 
    OrderID,
    OrderDate,
    DATEADD(YEAR, 2, OrderDate) AS TwoYearsLater
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05' AS DATE) AS OrderDate
    UNION ALL SELECT 2, CAST('2025-01-15' AS DATE)
    UNION ALL SELECT 3, CAST('2025-01-22' AS DATE)
    UNION ALL SELECT 4, CAST('2025-01-28' AS DATE)
    UNION ALL SELECT 5, CAST('2025-02-03' AS DATE)
    UNION ALL SELECT 6, CAST('2025-02-10' AS DATE)
    UNION ALL SELECT 7, CAST('2025-02-15' AS DATE)
    UNION ALL SELECT 8, CAST('2025-02-25' AS DATE)
    UNION ALL SELECT 9, CAST('2025-03-07' AS DATE)
    UNION ALL SELECT 10, CAST('2025-03-20' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | TwoYearsLater
--------|------------|---------------
1       | 2025-01-05 | 2027-01-05
2       | 2025-01-15 | 2027-01-15
3       | 2025-01-22 | 2027-01-22
4       | 2025-01-28 | 2027-01-28
5       | 2025-02-03 | 2027-02-03
6       | 2025-02-10 | 2027-02-10
7       | 2025-02-15 | 2027-02-15
8       | 2025-02-25 | 2027-02-25
9       | 2025-03-07 | 2027-03-07
10      | 2025-03-20 | 2027-03-20
```

Every date is now 2 years in the future!

---

### Example 8: Add 3 Months to All Orders

```sql
SELECT 
    OrderID,
    OrderDate,
    DATEADD(MONTH, 3, OrderDate) AS ThreeMonthsLater
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05' AS DATE) AS OrderDate
    UNION ALL SELECT 2, CAST('2025-01-15' AS DATE)
    UNION ALL SELECT 5, CAST('2025-02-03' AS DATE)
    UNION ALL SELECT 9, CAST('2025-03-07' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | ThreeMonthsLater
--------|------------|------------------
1       | 2025-01-05 | 2025-04-05
2       | 2025-01-15 | 2025-04-15
5       | 2025-02-03 | 2025-05-03
9       | 2025-03-07 | 2025-06-07
```

January → April, February → May, March → June!

---

### Example 9: Subtract 10 Days from All Orders

```sql
SELECT 
    OrderID,
    OrderDate,
    DATEADD(DAY, -10, OrderDate) AS TenDaysBefore
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05' AS DATE) AS OrderDate
    UNION ALL SELECT 2, CAST('2025-01-15' AS DATE)
    UNION ALL SELECT 7, CAST('2025-02-15' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | TenDaysBefore
--------|------------|---------------
1       | 2025-01-05 | 2024-12-26
2       | 2025-01-15 | 2025-01-05
7       | 2025-02-15 | 2025-02-05
```

Notice: Order 1 went back to December 2024 (previous year)!

---

## All Available Date Parts

You can add/subtract many different time units:

| Part | What It Adds/Subtracts | Example |
|------|------------------------|---------|
| YEAR or YY | Years | DATEADD(YEAR, 2, date) |
| QUARTER or QQ | Quarters (3 months) | DATEADD(QUARTER, 1, date) |
| MONTH or MM | Months | DATEADD(MONTH, 3, date) |
| WEEK or WK | Weeks (7 days) | DATEADD(WEEK, 2, date) |
| DAY or DD | Days | DATEADD(DAY, 10, date) |
| HOUR or HH | Hours | DATEADD(HOUR, 5, date) |
| MINUTE or MI | Minutes | DATEADD(MINUTE, 30, date) |
| SECOND or SS | Seconds | DATEADD(SECOND, 45, date) |

---

## Example 10: Using Different Time Parts

```sql
SELECT 
    CAST('2025-08-20 14:30:00' AS DATETIME) AS OriginalDateTime,
    DATEADD(HOUR, 5, '2025-08-20 14:30:00') AS Add5Hours,
    DATEADD(MINUTE, 30, '2025-08-20 14:30:00') AS Add30Minutes,
    DATEADD(SECOND, 45, '2025-08-20 14:30:00') AS Add45Seconds;
```

**Output:**
```
OriginalDateTime    | Add5Hours           | Add30Minutes        | Add45Seconds
--------------------|---------------------|---------------------|---------------------
2025-08-20 14:30:00 | 2025-08-20 19:30:00 | 2025-08-20 15:00:00 | 2025-08-20 14:30:45
```

---

## Real-World Use Cases

### Use Case 1: Calculate Due Dates

```sql
SELECT 
    OrderID,
    OrderDate,
    DATEADD(DAY, 30, OrderDate) AS PaymentDueDate,
    DATEADD(DAY, 7, OrderDate) AS ShippingDate
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15' AS DATE) AS OrderDate
    UNION ALL SELECT 2, CAST('2025-02-20' AS DATE)
    UNION ALL SELECT 3, CAST('2025-03-10' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | PaymentDueDate | ShippingDate
--------|------------|----------------|-------------
1       | 2025-01-15 | 2025-02-14     | 2025-01-22
2       | 2025-02-20 | 2025-03-22     | 2025-02-27
3       | 2025-03-10 | 2025-04-09     | 2025-03-17
```

Payment is due 30 days after order, shipping takes 7 days!

---

### Use Case 2: Find Orders from Last Month

```sql
SELECT 
    OrderID,
    OrderDate
FROM Orders
WHERE OrderDate >= DATEADD(MONTH, -1, GETDATE())
  AND OrderDate <= GETDATE();
```

This finds all orders from the last 30 days!

---

### Use Case 3: Calculate Subscription Expiry

```sql
SELECT 
    CustomerID,
    SubscriptionStart,
    DATEADD(YEAR, 1, SubscriptionStart) AS YearlyExpiry,
    DATEADD(MONTH, 1, SubscriptionStart) AS MonthlyExpiry
FROM (
    SELECT 1 AS CustomerID, CAST('2025-01-15' AS DATE) AS SubscriptionStart
    UNION ALL SELECT 2, CAST('2025-02-01' AS DATE)
) AS Subscriptions;
```

**Output:**
```
CustomerID | SubscriptionStart | YearlyExpiry | MonthlyExpiry
-----------|-------------------|--------------|---------------
1          | 2025-01-15        | 2026-01-15   | 2025-02-15
2          | 2025-02-01        | 2026-02-01   | 2025-03-01
```

---

## Complete Example: Multiple Date Calculations

```sql
SELECT 
    OrderID,
    OrderDate,
    
    -- Future dates
    DATEADD(YEAR, 1, OrderDate) AS NextYear,
    DATEADD(MONTH, 6, OrderDate) AS SixMonthsLater,
    DATEADD(WEEK, 2, OrderDate) AS TwoWeeksLater,
    DATEADD(DAY, 15, OrderDate) AS FifteenDaysLater,
    
    -- Past dates
    DATEADD(MONTH, -3, OrderDate) AS ThreeMonthsEarlier,
    DATEADD(DAY, -7, OrderDate) AS OneWeekEarlier
    
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15' AS DATE) AS OrderDate
    UNION ALL SELECT 2, CAST('2025-06-20' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | NextYear   | SixMonthsLater | TwoWeeksLater | FifteenDaysLater | ThreeMonthsEarlier | OneWeekEarlier
--------|------------|------------|----------------|---------------|------------------|--------------------|----------------
1       | 2025-01-15 | 2026-01-15 | 2025-07-15     | 2025-01-29    | 2025-01-30       | 2024-10-15         | 2025-01-08
2       | 2025-06-20 | 2026-06-20 | 2025-12-20     | 2025-07-04    | 2025-07-05       | 2025-03-20         | 2025-06-13
```

---

## Important Notes

### Positive vs Negative Numbers

```sql
DATEADD(DAY, 10, date)   -- Adds 10 days (future)
DATEADD(DAY, -10, date)  -- Subtracts 10 days (past)
```

The **sign** of the number controls whether you add or subtract!

---

### Month-End Behavior

When adding months, if the day doesn't exist, SQL adjusts:

```sql
SELECT DATEADD(MONTH, 1, '2025-01-31') AS Result;
```

**Output:**
```
Result
----------
2025-02-28
```

January 31 + 1 month = February 28 (since Feb 31 doesn't exist!)

---

## Quick Reference Guide

```sql
-- Add time (positive numbers)
DATEADD(YEAR, 2, date)      -- +2 years
DATEADD(MONTH, 3, date)     -- +3 months
DATEADD(DAY, 10, date)      -- +10 days
DATEADD(HOUR, 5, date)      -- +5 hours

-- Subtract time (negative numbers)
DATEADD(YEAR, -2, date)     -- -2 years
DATEADD(MONTH, -3, date)    -- -3 months
DATEADD(DAY, -10, date)     -- -10 days
DATEADD(HOUR, -5, date)     -- -5 hours
```

---

## Key Points to Remember

1. **Three parameters needed**: part, interval, date
2. **Positive = Add**, **Negative = Subtract**
3. Works with **years, months, days, hours, minutes, seconds**
4. Great for calculating **due dates, expiry dates, deadlines**
5. Automatically handles **month-end adjustments**
6. Can use with **WHERE clause** to filter dates
7. Very useful for **date range queries**

---

## Practice Exercise

Try these yourself:

```sql
-- 1. Add 90 days to today
SELECT DATEADD(DAY, 90, GETDATE()) AS NinetyDaysLater;

-- 2. Find date 6 months ago
SELECT DATEADD(MONTH, -6, GETDATE()) AS SixMonthsAgo;

-- 3. Calculate warranty expiry (1 year from purchase)
SELECT 
    'Product A' AS Product,
    '2025-01-15' AS PurchaseDate,
    DATEADD(YEAR, 1, '2025-01-15') AS WarrantyExpiry;
```

Now you understand DATEADD completely!
