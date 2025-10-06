# Understanding SQL DATEDIFF Function - Complete Guide

## What is DATEDIFF?

**DATEDIFF** stands for **Date Difference**. It calculates the difference between two dates and tells you how many years, months, days, hours, etc. are between them.

Think of it like measuring the distance between two points in time!

## Basic Syntax

```sql
DATEDIFF(part, start_date, end_date)
```

**Parameters:**
1. **part**: What you want to count (YEAR, MONTH, DAY, etc.)
2. **start_date**: The earlier/younger date (beginning point)
3. **end_date**: The later/older date (ending point)

**Formula**: end_date - start_date = difference

---

## How DATEDIFF Works - Visual Examples

**Order Date**: August 20, 2025  
**Shipping Date**: February 1, 2026

### Years Between Dates
```sql
DATEDIFF(YEAR, '2025-08-20', '2026-02-01') = 1 year
```

### Months Between Dates
```sql
DATEDIFF(MONTH, '2025-08-20', '2026-02-01') = 5 months
```

### Days Between Dates
```sql
DATEDIFF(DAY, '2025-08-20', '2026-02-01') = 165 days
```

---

## Example 1: Calculate Years Between Dates

```sql
SELECT 
    '2025-08-20' AS OrderDate,
    '2026-02-01' AS ShippingDate,
    DATEDIFF(YEAR, '2025-08-20', '2026-02-01') AS YearsDifference;
```

**Output:**
```
OrderDate  | ShippingDate | YearsDifference
-----------|--------------|----------------
2025-08-20 | 2026-02-01   | 0
```

Note: It's 0 because there's less than a full year between them!

---

## Example 2: Calculate Months Between Dates

```sql
SELECT 
    '2025-08-20' AS OrderDate,
    '2026-02-01' AS ShippingDate,
    DATEDIFF(MONTH, '2025-08-20', '2026-02-01') AS MonthsDifference;
```

**Output:**
```
OrderDate  | ShippingDate | MonthsDifference
-----------|--------------|------------------
2025-08-20 | 2026-02-01   | 5
```

From August to February = 5 months!

---

## Example 3: Calculate Days Between Dates

```sql
SELECT 
    '2025-08-20' AS OrderDate,
    '2026-02-01' AS ShippingDate,
    DATEDIFF(DAY, '2025-08-20', '2026-02-01') AS DaysDifference;
```

**Output:**
```
OrderDate  | ShippingDate | DaysDifference
-----------|--------------|----------------
2025-08-20 | 2026-02-01   | 165
```

165 days between these dates!

---

## Real-World Example 1: Calculate Employee Age

**Task**: Find how old each employee is based on their birthday.

```sql
SELECT 
    EmployeeID,
    BirthDate,
    GETDATE() AS Today,
    DATEDIFF(YEAR, BirthDate, GETDATE()) AS Age
FROM (
    SELECT 1 AS EmployeeID, CAST('1991-05-15' AS DATE) AS BirthDate
    UNION ALL SELECT 2, CAST('1972-08-22' AS DATE)
    UNION ALL SELECT 3, CAST('1985-03-10' AS DATE)
    UNION ALL SELECT 4, CAST('1990-11-30' AS DATE)
) AS Employees;
```

**Output (assuming today is October 7, 2024):**
```
EmployeeID | BirthDate  | Today      | Age
-----------|------------|------------|----
1          | 1991-05-15 | 2024-10-07 | 33
2          | 1972-08-22 | 2024-10-07 | 52
3          | 1985-03-10 | 2024-10-07 | 39
4          | 1990-11-30 | 2024-10-07 | 33
```

Perfect for calculating ages!

---

## Real-World Example 2: Shipping Duration

**Task**: Find how many days it takes to ship each order.

```sql
SELECT 
    OrderID,
    OrderDate,
    ShipDate,
    DATEDIFF(DAY, OrderDate, ShipDate) AS DaysToShip
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-01' AS DATE) AS OrderDate, CAST('2025-01-05' AS DATE) AS ShipDate
    UNION ALL SELECT 2, CAST('2025-01-05' AS DATE), CAST('2025-01-12' AS DATE)
    UNION ALL SELECT 3, CAST('2025-01-15' AS DATE), CAST('2025-01-30' AS DATE)
    UNION ALL SELECT 4, CAST('2025-01-20' AS DATE), CAST('2025-01-25' AS DATE)
    UNION ALL SELECT 5, CAST('2025-02-01' AS DATE), CAST('2025-02-10' AS DATE)
    UNION ALL SELECT 6, CAST('2025-02-05' AS DATE), CAST('2025-02-12' AS DATE)
    UNION ALL SELECT 7, CAST('2025-02-15' AS DATE), CAST('2025-02-22' AS DATE)
    UNION ALL SELECT 8, CAST('2025-02-20' AS DATE), CAST('2025-02-26' AS DATE)
    UNION ALL SELECT 9, CAST('2025-03-01' AS DATE), CAST('2025-03-05' AS DATE)
    UNION ALL SELECT 10, CAST('2025-03-10' AS DATE), CAST('2025-03-16' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | ShipDate   | DaysToShip
--------|------------|------------|------------
1       | 2025-01-01 | 2025-01-05 | 4
2       | 2025-01-05 | 2025-01-12 | 7
3       | 2025-01-15 | 2025-01-30 | 15
4       | 2025-01-20 | 2025-01-25 | 5
5       | 2025-02-01 | 2025-02-10 | 9
6       | 2025-02-05 | 2025-02-12 | 7
7       | 2025-02-15 | 2025-02-22 | 7
8       | 2025-02-20 | 2025-02-26 | 6
9       | 2025-03-01 | 2025-03-05 | 4
10      | 2025-03-10 | 2025-03-16 | 6
```

Now you know how long each order took to ship!

---

## Real-World Example 3: Average Shipping Duration by Month

**Task**: Find the average shipping time for each month.

```sql
SELECT 
    DATENAME(MONTH, OrderDate) AS OrderMonth,
    AVG(DATEDIFF(DAY, OrderDate, ShipDate)) AS AvgShippingDays
FROM (
    SELECT CAST('2025-01-01' AS DATE) AS OrderDate, CAST('2025-01-05' AS DATE) AS ShipDate
    UNION ALL SELECT CAST('2025-01-05' AS DATE), CAST('2025-01-12' AS DATE)
    UNION ALL SELECT CAST('2025-01-15' AS DATE), CAST('2025-01-30' AS DATE)
    UNION ALL SELECT CAST('2025-01-20' AS DATE), CAST('2025-01-25' AS DATE)
    UNION ALL SELECT CAST('2025-02-01' AS DATE), CAST('2025-02-10' AS DATE)
    UNION ALL SELECT CAST('2025-02-05' AS DATE), CAST('2025-02-12' AS DATE)
    UNION ALL SELECT CAST('2025-02-15' AS DATE), CAST('2025-02-22' AS DATE)
    UNION ALL SELECT CAST('2025-02-20' AS DATE), CAST('2025-02-26' AS DATE)
    UNION ALL SELECT CAST('2025-03-01' AS DATE), CAST('2025-03-05' AS DATE)
    UNION ALL SELECT CAST('2025-03-10' AS DATE), CAST('2025-03-16' AS DATE)
) AS Orders
GROUP BY DATENAME(MONTH, OrderDate), MONTH(OrderDate)
ORDER BY MONTH(MIN(OrderDate));
```

**Output:**
```
OrderMonth | AvgShippingDays
-----------|----------------
January    | 7
February   | 7
March      | 5
```

March has the fastest shipping!

---

## Real-World Example 4: Time Gap Analysis (Days Between Orders)

**Task**: Find how many days passed between each order and the previous order.

This uses the **LAG** window function combined with DATEDIFF!

```sql
SELECT 
    OrderID,
    OrderDate,
    LAG(OrderDate) OVER (ORDER BY OrderDate) AS PreviousOrderDate,
    DATEDIFF(DAY, 
             LAG(OrderDate) OVER (ORDER BY OrderDate), 
             OrderDate) AS DaysSincePreviousOrder
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-01' AS DATE) AS OrderDate
    UNION ALL SELECT 2, CAST('2025-01-05' AS DATE)
    UNION ALL SELECT 3, CAST('2025-01-15' AS DATE)
    UNION ALL SELECT 4, CAST('2025-01-20' AS DATE)
    UNION ALL SELECT 5, CAST('2025-02-01' AS DATE)
    UNION ALL SELECT 6, CAST('2025-02-05' AS DATE)
    UNION ALL SELECT 7, CAST('2025-02-15' AS DATE)
    UNION ALL SELECT 8, CAST('2025-02-20' AS DATE)
    UNION ALL SELECT 9, CAST('2025-03-01' AS DATE)
    UNION ALL SELECT 10, CAST('2025-03-10' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | PreviousOrderDate | DaysSincePreviousOrder
--------|------------|-------------------|------------------------
1       | 2025-01-01 | NULL              | NULL
2       | 2025-01-05 | 2025-01-01        | 4
3       | 2025-01-15 | 2025-01-05        | 10
4       | 2025-01-20 | 2025-01-15        | 5
5       | 2025-02-01 | 2025-01-20        | 12
6       | 2025-02-05 | 2025-02-01        | 4
7       | 2025-02-15 | 2025-02-05        | 10
8       | 2025-02-20 | 2025-02-15        | 5
9       | 2025-03-01 | 2025-02-20        | 9
10      | 2025-03-10 | 2025-03-01        | 9
```

This is called **Time Gap Analysis** - super useful for business!

---

## All Available Date Parts

| Part | What It Counts | Example |
|------|----------------|---------|
| YEAR or YY | Years | DATEDIFF(YEAR, date1, date2) |
| QUARTER or QQ | Quarters | DATEDIFF(QUARTER, date1, date2) |
| MONTH or MM | Months | DATEDIFF(MONTH, date1, date2) |
| WEEK or WK | Weeks | DATEDIFF(WEEK, date1, date2) |
| DAY or DD | Days | DATEDIFF(DAY, date1, date2) |
| HOUR or HH | Hours | DATEDIFF(HOUR, date1, date2) |
| MINUTE or MI | Minutes | DATEDIFF(MINUTE, date1, date2) |
| SECOND or SS | Seconds | DATEDIFF(SECOND, date1, date2) |

---

## Example 5: Different Time Units

```sql
SELECT 
    '2025-01-15 10:30:00' AS StartDateTime,
    '2025-03-20 15:45:30' AS EndDateTime,
    DATEDIFF(MONTH, '2025-01-15 10:30:00', '2025-03-20 15:45:30') AS Months,
    DATEDIFF(DAY, '2025-01-15 10:30:00', '2025-03-20 15:45:30') AS Days,
    DATEDIFF(HOUR, '2025-01-15 10:30:00', '2025-03-20 15:45:30') AS Hours,
    DATEDIFF(MINUTE, '2025-01-15 10:30:00', '2025-03-20 15:45:30') AS Minutes;
```

**Output:**
```
StartDateTime       | EndDateTime         | Months | Days | Hours | Minutes
--------------------|---------------------|--------|------|-------|--------
2025-01-15 10:30:00 | 2025-03-20 15:45:30 | 2      | 64   | 1541  | 92475
```

Same time period, different measurements!

---

## Negative Results (When Dates are Backwards)

If the start date is AFTER the end date, you get a negative number!

```sql
SELECT 
    DATEDIFF(DAY, '2025-03-20', '2025-01-15') AS NegativeDays;
```

**Output:**
```
NegativeDays
------------
-64
```

The result is negative because we're going backwards in time!

---

## Common Use Cases Summary

### 1. Calculate Age
```sql
DATEDIFF(YEAR, BirthDate, GETDATE())
```

### 2. Find Duration
```sql
DATEDIFF(DAY, StartDate, EndDate)
```

### 3. Filter Recent Records
```sql
WHERE DATEDIFF(DAY, OrderDate, GETDATE()) <= 30
```

### 4. Calculate Membership Length
```sql
DATEDIFF(MONTH, JoinDate, GETDATE())
```

### 5. Find Overdue Items
```sql
DATEDIFF(DAY, DueDate, GETDATE()) AS DaysOverdue
```

---

## Complete Business Example

```sql
SELECT 
    OrderID,
    OrderDate,
    ShipDate,
    PaymentDate,
    
    -- How long to ship?
    DATEDIFF(DAY, OrderDate, ShipDate) AS ShippingDays,
    
    -- How long to pay?
    DATEDIFF(DAY, OrderDate, PaymentDate) AS PaymentDays,
    
    -- Categorize shipping speed
    CASE 
        WHEN DATEDIFF(DAY, OrderDate, ShipDate) <= 3 THEN 'Fast'
        WHEN DATEDIFF(DAY, OrderDate, ShipDate) <= 7 THEN 'Normal'
        ELSE 'Slow'
    END AS ShippingSpeed
    
FROM (
    SELECT 1 AS OrderID, 
           CAST('2025-01-01' AS DATE) AS OrderDate, 
           CAST('2025-01-05' AS DATE) AS ShipDate,
           CAST('2025-01-10' AS DATE) AS PaymentDate
    UNION ALL SELECT 2, CAST('2025-01-05' AS DATE), CAST('2025-01-08' AS DATE), CAST('2025-01-15' AS DATE)
    UNION ALL SELECT 3, CAST('2025-01-15' AS DATE), CAST('2025-01-30' AS DATE), CAST('2025-02-05' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | ShipDate   | PaymentDate | ShippingDays | PaymentDays | ShippingSpeed
--------|------------|------------|-------------|--------------|-------------|---------------
1       | 2025-01-01 | 2025-01-05 | 2025-01-10  | 4            | 9           | Normal
2       | 2025-01-05 | 2025-01-08 | 2025-01-15  | 3            | 10          | Fast
3       | 2025-01-15 | 2025-01-30 | 2025-02-05  | 15           | 21          | Slow
```

---

## Key Points to Remember

1. **Order matters**: start_date comes first, end_date comes second
2. **Returns a number**: Always gives you an integer
3. **Can be negative**: If start_date is after end_date
4. **Counts boundaries**: DATEDIFF(DAY, '2025-01-01', '2025-01-02') = 1
5. **Perfect for analytics**: Age, duration, time gaps, etc.
6. **Combine with other functions**: LAG, AVG, GETDATE, etc.
7. **Different units available**: Years, months, days, hours, minutes, seconds

---

## Quick Reference

```sql
-- Calculate age
DATEDIFF(YEAR, BirthDate, GETDATE())

-- Days between dates
DATEDIFF(DAY, StartDate, EndDate)

-- Months of membership
DATEDIFF(MONTH, JoinDate, GETDATE())

-- Hours worked
DATEDIFF(HOUR, ClockIn, ClockOut)

-- Filter last 30 days
WHERE DATEDIFF(DAY, OrderDate, GETDATE()) <= 30
```

---

## Practice Exercise

Try these yourself:

```sql
-- 1. How old are you?
SELECT DATEDIFF(YEAR, '2010-01-01', GETDATE()) AS MyAge;

-- 2. Days until New Year
SELECT DATEDIFF(DAY, GETDATE(), '2026-01-01') AS DaysUntilNewYear;

-- 3. How long have you been learning SQL?
SELECT DATEDIFF(DAY, '2025-01-01', GETDATE()) AS DaysLearning;
```

Now you understand DATEDIFF completely and can use it for powerful date analysis!
