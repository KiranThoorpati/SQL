# SQL DATEPART Function - Complete Guide

## What is DATEPART?

DATEPART is a **super powerful function** that can extract ANY part from a date - not just day, month, and year!

Think of a date like a treasure chest with hidden information inside. DATEPART is the key that unlocks ALL the treasures! 🗝️

---

## Why Do We Need DATEPART?

We already learned DAY(), MONTH(), and YEAR() functions. But what if we want to know:
- Which **quarter** of the year? (Q1, Q2, Q3, Q4)
- Which **week** number? (Week 1, Week 2, etc.)
- What **hour** of the day?
- Which **day of the week**? (Monday, Tuesday, etc.)

**DATEPART can do ALL of this!** 🎯

---

## Basic Syntax

```sql
DATEPART(part, date)
```

**Two parameters:**
1. **part** - What piece do you want? (year, month, quarter, week, hour, etc.)
2. **date** - Which date to extract from?

---

## Simple Examples

### Example 1: Extracting Year, Month, Day (Like Before)

```sql
SELECT 
    '2025-08-20' AS FullDate,
    DATEPART(YEAR, '2025-08-20') AS Year,
    DATEPART(MONTH, '2025-08-20') AS Month,
    DATEPART(DAY, '2025-08-20') AS Day;
```

**Output:**
```
FullDate     Year  Month  Day
----------   ----  -----  ---
2025-08-20   2025  8      20
```

**Note:** This gives the **same result** as YEAR(), MONTH(), and DAY() functions!

---

### Example 2: Extracting NEW Information - Quarter

```sql
SELECT 
    '2025-08-20' AS FullDate,
    DATEPART(QUARTER, '2025-08-20') AS Quarter;
```

**Output:**
```
FullDate     Quarter
----------   -------
2025-08-20   3
```

**What does this mean?**
- Quarter 1 = January, February, March
- Quarter 2 = April, May, June
- Quarter 3 = July, August, September ← **August is in Q3!**
- Quarter 4 = October, November, December

---

### Example 3: Extracting Week Number

```sql
SELECT 
    '2025-08-20' AS FullDate,
    DATEPART(WEEK, '2025-08-20') AS WeekNumber;
```

**Output:**
```
FullDate     WeekNumber
----------   ----------
2025-08-20   34
```

**Meaning:** August 20, 2025 falls in the **34th week** of the year!

---

### Example 4: Extracting Time Parts (Hour, Minute, Second)

```sql
SELECT 
    '2025-08-20 14:35:45' AS DateTime,
    DATEPART(HOUR, '2025-08-20 14:35:45') AS Hour,
    DATEPART(MINUTE, '2025-08-20 14:35:45') AS Minute,
    DATEPART(SECOND, '2025-08-20 14:35:45') AS Second;
```

**Output:**
```
DateTime             Hour  Minute  Second
-------------------  ----  ------  ------
2025-08-20 14:35:45  14    35      45
```

---

### Example 5: Day of the Week

```sql
SELECT 
    '2025-08-20' AS FullDate,
    DATEPART(WEEKDAY, '2025-08-20') AS DayOfWeek;
```

**Output:**
```
FullDate     DayOfWeek
----------   ---------
2025-08-20   4
```

**What the numbers mean:**
- 1 = Sunday
- 2 = Monday
- 3 = Tuesday
- 4 = Wednesday ← **August 20, 2025 is a Wednesday!**
- 5 = Thursday
- 6 = Friday
- 7 = Saturday

---

## Real Database Example

Let's work with a table of orders with creation times:

```sql
SELECT 
    OrderID,
    CreationTime,
    DATEPART(YEAR, CreationTime) AS Year,
    DATEPART(MONTH, CreationTime) AS Month,
    DATEPART(DAY, CreationTime) AS Day,
    DATEPART(HOUR, CreationTime) AS Hour,
    DATEPART(QUARTER, CreationTime) AS Quarter,
    DATEPART(WEEK, CreationTime) AS WeekNumber,
    DATEPART(WEEKDAY, CreationTime) AS DayOfWeek
FROM (
    VALUES 
        (101, '2025-01-15 12:30:45'),
        (102, '2025-01-22 23:15:30'),
        (103, '2025-02-10 08:45:20')
) AS Orders(OrderID, CreationTime);
```

**Output:**
```
OrderID  CreationTime         Year  Month  Day  Hour  Quarter  WeekNumber  DayOfWeek
-------  -------------------  ----  -----  ---  ----  -------  ----------  ---------
101      2025-01-15 12:30:45  2025  1      15   12    1        3           4
102      2025-01-22 23:15:30  2025  1      22   23    1        4           4
103      2025-02-10 08:45:20  2025  2      10   8     1        7           2
```

**What can we learn?**
- All orders are from **Quarter 1** (January-March)
- Order 101 was placed at **12 PM** (noon)
- Order 102 was placed at **11 PM** (late night!)
- Order 103 was placed on **Monday** (DayOfWeek = 2)

---

## All Available Parts You Can Extract

| Part Name | What It Returns | Example Output |
|-----------|----------------|----------------|
| **YEAR** | 4-digit year | 2025 |
| **MONTH** | Month number (1-12) | 8 |
| **DAY** | Day of month (1-31) | 20 |
| **QUARTER** | Quarter (1-4) | 3 |
| **WEEK** | Week of year (1-53) | 34 |
| **WEEKDAY** | Day of week (1-7) | 4 |
| **HOUR** | Hour (0-23) | 14 |
| **MINUTE** | Minute (0-59) | 35 |
| **SECOND** | Second (0-59) | 45 |
| **DAYOFYEAR** | Day of year (1-366) | 232 |

---

## Real-World Use Cases

### Use Case 1: Quarterly Sales Report

```sql
SELECT 
    DATEPART(QUARTER, OrderDate) AS Quarter,
    COUNT(*) AS TotalOrders
FROM (
    VALUES 
        (1, '2025-01-15'),
        (2, '2025-03-20'),
        (3, '2025-07-10'),
        (4, '2025-10-05')
) AS Orders(OrderID, OrderDate)
GROUP BY DATEPART(QUARTER, OrderDate)
ORDER BY Quarter;
```

**Output:**
```
Quarter  TotalOrders
-------  -----------
1        2
3        1
4        1
```

---

### Use Case 2: Finding Peak Shopping Hours

```sql
SELECT 
    DATEPART(HOUR, OrderTime) AS ShoppingHour,
    COUNT(*) AS OrderCount
FROM (
    VALUES 
        ('2025-08-20 09:00:00'),
        ('2025-08-20 09:30:00'),
        ('2025-08-20 14:00:00'),
        ('2025-08-20 14:15:00'),
        ('2025-08-20 14:45:00')
) AS Orders(OrderTime)
GROUP BY DATEPART(HOUR, OrderTime)
ORDER BY OrderCount DESC;
```

**Output:**
```
ShoppingHour  OrderCount
------------  ----------
14            3
9             2
```

**Insight:** Most orders come at **2 PM** (hour 14)!

---

### Use Case 3: Weekend vs Weekday Analysis

```sql
SELECT 
    OrderDate,
    DATEPART(WEEKDAY, OrderDate) AS DayNumber,
    CASE 
        WHEN DATEPART(WEEKDAY, OrderDate) IN (1, 7) 
        THEN 'Weekend' 
        ELSE 'Weekday' 
    END AS DayType
FROM (
    VALUES 
        ('2025-08-16'),  -- Saturday
        ('2025-08-17'),  -- Sunday
        ('2025-08-18')   -- Monday
) AS Orders(OrderDate);
```

**Output:**
```
OrderDate   DayNumber  DayType
----------  ---------  -------
2025-08-16  7          Weekend
2025-08-17  1          Weekend
2025-08-18  2          Weekday
```

---

## Important Notes

✅ **All outputs are INTEGERS** (whole numbers)  
✅ **Works with DATE and DATETIME** columns  
✅ **Can extract hidden information** (quarter, week, etc.)  
✅ **Perfect for grouping and filtering** data  
✅ **More powerful than DAY/MONTH/YEAR** functions  

---

## DATEPART vs DAY/MONTH/YEAR

| Feature | DAY/MONTH/YEAR | DATEPART |
|---------|----------------|----------|
| Extract year, month, day | ✅ Yes | ✅ Yes |
| Extract quarter, week | ❌ No | ✅ Yes |
| Extract hour, minute, second | ❌ No | ✅ Yes |
| Extract day of week | ❌ No | ✅ Yes |

**Conclusion:** DATEPART is the **Swiss Army knife** of date functions! 🔧

---

## Practice Challenge 🎯

Find out which quarter YOUR birthday falls in:

```sql
SELECT 
    '2010-05-15' AS MyBirthday,  -- Change to YOUR birthday
    DATEPART(QUARTER, '2010-05-15') AS BirthdayQuarter,
    DATEPART(WEEK, '2010-05-15') AS BirthdayWeek,
    DATEPART(WEEKDAY, '2010-05-15') AS DayOfWeek;
```

---

## Summary

DATEPART is your **ultimate date extraction tool**! Use it when you need:
- 📊 Quarterly reports
- 📅 Week-by-week analysis
- ⏰ Hour-by-hour trends
- 📈 Day-of-week patterns

**Remember:** DATEPART(what_you_want, from_which_date) 🎉
