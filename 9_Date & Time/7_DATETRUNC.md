# Understanding SQL DATETRUNC Function

## What is DATETRUNC?

**DATETRUNC** (Date Truncate) is a special SQL function that helps us control how detailed our date information should be. Think of it like a "zoom" feature for dates - you can zoom out to see only years, or zoom in to see months, days, hours, and even seconds!

## How is DATETRUNC Different?

- **DATEPART**: Extracts a number (like 8 for August)
- **DATENAME**: Extracts a name (like "August")
- **DATETRUNC**: Keeps the date but **resets** the parts you don't need!

## Basic Syntax

```sql
DATETRUNC(part, date)
```

- **part**: The level of detail you want (year, month, day, hour, minute, second)
- **date**: The date/time you're working with

## Understanding the Date Hierarchy

Dates have a hierarchy from **highest to lowest**:

```
Year → Month → Day → Hour → Minute → Second
(Least detailed)              (Most detailed)
```

---

## How DATETRUNC Works - Step by Step

Let's start with this date and time: **2024-08-20 14:35:55**

### Example 1: Truncate to MINUTE Level

```sql
SELECT DATETRUNC(MINUTE, '2024-08-20 14:35:55') AS TruncatedToMinute;
```

**What happens?**
- Keeps: Year, Month, Day, Hour, Minute
- Resets: **Seconds → 00**

**Output:**
```
TruncatedToMinute
-------------------
2024-08-20 14:35:00
```

---

### Example 2: Truncate to HOUR Level

```sql
SELECT DATETRUNC(HOUR, '2024-08-20 14:35:55') AS TruncatedToHour;
```

**What happens?**
- Keeps: Year, Month, Day, Hour
- Resets: **Minutes → 00, Seconds → 00**

**Output:**
```
TruncatedToHour
-------------------
2024-08-20 14:00:00
```

---

### Example 3: Truncate to DAY Level

```sql
SELECT DATETRUNC(DAY, '2024-08-20 14:35:55') AS TruncatedToDay;
```

**What happens?**
- Keeps: Year, Month, Day
- Resets: **Entire time → 00:00:00**

**Output:**
```
TruncatedToDay
-------------------
2024-08-20 00:00:00
```

---

### Example 4: Truncate to MONTH Level

```sql
SELECT DATETRUNC(MONTH, '2024-08-20 14:35:55') AS TruncatedToMonth;
```

**What happens?**
- Keeps: Year, Month
- Resets: **Day → 01, Time → 00:00:00**

**Output:**
```
TruncatedToMonth
-------------------
2024-08-01 00:00:00
```

**Note**: Days start from 01 (not 00), so it goes to the 1st day of the month!

---

### Example 5: Truncate to YEAR Level

```sql
SELECT DATETRUNC(YEAR, '2024-08-20 14:35:55') AS TruncatedToYear;
```

**What happens?**
- Keeps: Year only
- Resets: **Month → 01, Day → 01, Time → 00:00:00**

**Output:**
```
TruncatedToYear
-------------------
2024-01-01 00:00:00
```

Goes to January 1st of that year!

---

## Complete Example with All Levels

```sql
SELECT 
    '2024-08-20 14:35:55' AS OriginalDateTime,
    DATETRUNC(YEAR, '2024-08-20 14:35:55') AS Year_Level,
    DATETRUNC(MONTH, '2024-08-20 14:35:55') AS Month_Level,
    DATETRUNC(DAY, '2024-08-20 14:35:55') AS Day_Level,
    DATETRUNC(HOUR, '2024-08-20 14:35:55') AS Hour_Level,
    DATETRUNC(MINUTE, '2024-08-20 14:35:55') AS Minute_Level;
```

**Output:**
```
OriginalDateTime    | Year_Level          | Month_Level         | Day_Level           | Hour_Level          | Minute_Level
--------------------|---------------------|---------------------|---------------------|---------------------|---------------------
2024-08-20 14:35:55 | 2024-01-01 00:00:00 | 2024-08-01 00:00:00 | 2024-08-20 00:00:00 | 2024-08-20 14:00:00 | 2024-08-20 14:35:00
```

---

## Real-World Example: Sales Orders Analysis

Let's say we have a **SalesOrders** table:

```sql
OrderID | CreationTime
--------|--------------------
1       | 2025-01-05 10:30:45
2       | 2025-01-15 14:20:30
3       | 2025-01-22 09:15:55
4       | 2025-01-28 16:45:10
5       | 2025-02-03 11:30:20
6       | 2025-02-10 13:50:35
7       | 2025-02-18 15:25:40
8       | 2025-02-25 08:40:15
9       | 2025-03-07 12:10:50
10      | 2025-03-20 17:30:25
```

### Problem 1: Count Orders Per Second (Too Detailed!)

```sql
SELECT 
    CreationTime,
    COUNT(*) AS NumberOfOrders
FROM SalesOrders
GROUP BY CreationTime;
```

**Output:**
```
CreationTime        | NumberOfOrders
--------------------|---------------
2025-01-05 10:30:45 | 1
2025-01-15 14:20:30 | 1
2025-01-22 09:15:55 | 1
...                 | 1
```

**Problem**: Every order shows count = 1 because the times are too precise (down to seconds)!

---

### Solution 1: Truncate to MONTH Level

```sql
SELECT 
    DATETRUNC(MONTH, CreationTime) AS Month,
    COUNT(*) AS NumberOfOrders
FROM SalesOrders
GROUP BY DATETRUNC(MONTH, CreationTime);
```

**Output:**
```
Month               | NumberOfOrders
--------------------|---------------
2025-01-01 00:00:00 | 4
2025-02-01 00:00:00 | 4
2025-03-01 00:00:00 | 2
```

**Much better!** Now we can see:
- January: 4 orders
- February: 4 orders  
- March: 2 orders

---

### Solution 2: Truncate to YEAR Level

```sql
SELECT 
    DATETRUNC(YEAR, CreationTime) AS Year,
    COUNT(*) AS NumberOfOrders
FROM SalesOrders
GROUP BY DATETRUNC(YEAR, CreationTime);
```

**Output:**
```
Year                | NumberOfOrders
--------------------|---------------
2025-01-01 00:00:00 | 10
```

Now we're zoomed out to see the total orders for the entire year!

---

## Why is DATETRUNC Amazing?

### 🎯 Quick Analysis at Different Levels

You can easily switch between different levels of detail:

```sql
-- Want weekly analysis? Change to WEEK
SELECT DATETRUNC(WEEK, CreationTime), COUNT(*)
FROM SalesOrders
GROUP BY DATETRUNC(WEEK, CreationTime);

-- Want daily analysis? Change to DAY
SELECT DATETRUNC(DAY, CreationTime), COUNT(*)
FROM SalesOrders
GROUP BY DATETRUNC(DAY, CreationTime);

-- Want yearly analysis? Change to YEAR
SELECT DATETRUNC(YEAR, CreationTime), COUNT(*)
FROM SalesOrders
GROUP BY DATETRUNC(YEAR, CreationTime);
```

It's like having a **zoom in / zoom out** button for your data!

---

## Visual Summary

```
Original: 2024-08-20 14:35:55

DATETRUNC(YEAR)   → 2024-01-01 00:00:00  (Zoomed way out)
DATETRUNC(MONTH)  → 2024-08-01 00:00:00  (Zoomed out)
DATETRUNC(DAY)    → 2024-08-20 00:00:00  (Normal view)
DATETRUNC(HOUR)   → 2024-08-20 14:00:00  (Zoomed in)
DATETRUNC(MINUTE) → 2024-08-20 14:35:00  (More zoomed in)
Original          → 2024-08-20 14:35:55  (Most detailed)
```

---

## Key Points to Remember

1. **DATETRUNC keeps the date** but resets parts you don't need
2. **Output is always a date/time**, not a number or text
3. It helps you **control the level of detail** in your analysis
4. Perfect for **grouping data** by different time periods
5. You can easily **switch between year, month, day, hour** analysis
6. Great for creating **reports and summaries** at different levels

---

## Practice Exercise

Try this query to see the current date truncated at different levels:

```sql
SELECT 
    GETDATE() AS CurrentDateTime,
    DATETRUNC(YEAR, GETDATE()) AS Year_Level,
    DATETRUNC(MONTH, GETDATE()) AS Month_Level,
    DATETRUNC(DAY, GETDATE()) AS Day_Level;
```

This will show you how DATETRUNC works with today's date!
