# SQL DAY, MONTH, YEAR Functions - Simple Guide

## The Three Easiest Date Functions!

These are super simple functions that pull out specific parts from a date:

- **DAY()** - Gets the day number
- **MONTH()** - Gets the month number  
- **YEAR()** - Gets the year number

---

## How They Work

Imagine you have this date: **2025-08-20** (August 20, 2025)

```
2025  -  08  -  20
 ↓       ↓      ↓
YEAR   MONTH   DAY
```

Each function extracts one piece!

---

## Basic Syntax

All three functions work the same way - just put the date inside:

```sql
DAY(your_date)
MONTH(your_date)
YEAR(your_date)
```

---

## Simple Examples

### Example 1: Extracting Each Part

```sql
SELECT 
    '2025-08-20' AS FullDate,
    DAY('2025-08-20') AS DayPart,
    MONTH('2025-08-20') AS MonthPart,
    YEAR('2025-08-20') AS YearPart;
```

**Output:**
```
FullDate     DayPart  MonthPart  YearPart
----------   -------  ---------  --------
2025-08-20   20       8          2025
```

**What happened?**
- DAY extracted **20**
- MONTH extracted **8** (August is the 8th month)
- YEAR extracted **2025**

---

## Working with a Real Table

Let's say we have an orders table with dates:

### Example 2: Extracting from Multiple Orders

```sql
SELECT 
    OrderID,
    CreationTime,
    YEAR(CreationTime) AS OrderYear,
    MONTH(CreationTime) AS OrderMonth,
    DAY(CreationTime) AS OrderDay
FROM (
    VALUES 
        (101, '2025-01-15 09:30:45'),
        (102, '2025-02-20 14:15:30'),
        (103, '2025-03-10 11:45:20')
) AS Orders(OrderID, CreationTime);
```

**Output:**
```
OrderID  CreationTime         OrderYear  OrderMonth  OrderDay
-------  -------------------  ---------  ----------  --------
101      2025-01-15 09:30:45  2025       1           15
102      2025-02-20 14:15:30  2025       2           20
103      2025-03-10 11:45:20  2025       3           10
```

**Notice:** Even though CreationTime includes the time (hours, minutes, seconds), the functions only extract the date parts!

---

## Real-World Use Cases

### Use Case 1: Finding All Orders from a Specific Month

```sql
SELECT 
    OrderID,
    CreationTime,
    MONTH(CreationTime) AS MonthNumber
FROM (
    VALUES 
        (101, '2025-01-15 09:30:45'),
        (102, '2025-02-20 14:15:30'),
        (103, '2025-02-05 11:45:20'),
        (104, '2025-02-28 16:20:10')
) AS Orders(OrderID, CreationTime)
WHERE MONTH(CreationTime) = 2;  -- Only February orders
```

**Output:**
```
OrderID  CreationTime         MonthNumber
-------  -------------------  -----------
102      2025-02-20 14:15:30  2
103      2025-02-05 11:45:20  2
104      2025-02-28 16:20:10  2
```

---

### Use Case 2: Counting Orders by Year

```sql
SELECT 
    YEAR(CreationTime) AS OrderYear,
    COUNT(*) AS TotalOrders
FROM (
    VALUES 
        (101, '2024-01-15 09:30:45'),
        (102, '2024-02-20 14:15:30'),
        (103, '2025-03-10 11:45:20'),
        (104, '2025-04-15 16:20:10')
) AS Orders(OrderID, CreationTime)
GROUP BY YEAR(CreationTime);
```

**Output:**
```
OrderYear  TotalOrders
---------  -----------
2024       2
2025       2
```

---

### Use Case 3: Student Birthdays in Specific Month

```sql
SELECT 
    StudentName,
    Birthday,
    MONTH(Birthday) AS BirthMonth,
    DAY(Birthday) AS BirthDay
FROM (
    VALUES 
        ('Ravi', '2010-08-15'),
        ('Priya', '2010-08-22'),
        ('Amit', '2010-12-03')
) AS Students(StudentName, Birthday)
WHERE MONTH(Birthday) = 8;  -- August birthdays only
```

**Output:**
```
StudentName  Birthday    BirthMonth  BirthDay
-----------  ----------  ----------  --------
Ravi         2010-08-15  8           15
Priya        2010-08-22  8           22
```

---

## All Three Functions Together

### Example: Complete Date Breakdown

```sql
SELECT 
    'Project Deadline' AS Description,
    '2025-12-25' AS FullDate,
    YEAR('2025-12-25') AS Year,
    MONTH('2025-12-25') AS Month,
    DAY('2025-12-25') AS Day,
    'Christmas Day!' AS Note;
```

**Output:**
```
Description        FullDate    Year  Month  Day  Note
----------------   ----------  ----  -----  ---  --------------
Project Deadline   2025-12-25  2025  12     25   Christmas Day!
```

---

## Quick Reference

| Function | What It Returns | Example Input | Example Output |
|----------|----------------|---------------|----------------|
| **YEAR()** | 4-digit year | '2025-08-20' | 2025 |
| **MONTH()** | Month number (1-12) | '2025-08-20' | 8 |
| **DAY()** | Day number (1-31) | '2025-08-20' | 20 |

---

## Important Notes

✅ **Works with DATE or DATETIME** - Both work fine!  
✅ **Returns numbers** - Not text (8, not "August")  
✅ **Only one parameter** - Just pass the date  
✅ **Great for filtering** - Find records by month, year, or day  
✅ **Perfect for grouping** - Count or sum data by time periods  

---

## Practice Exercise 🎯

Try extracting parts from your own birthday:

```sql
SELECT 
    'My Birthday' AS Event,
    '2010-05-15' AS MyBirthday,  -- Change this to YOUR birthday!
    YEAR('2010-05-15') AS BirthYear,
    MONTH('2010-05-15') AS BirthMonth,
    DAY('2010-05-15') AS BirthDay;
```

---

## Summary

These three functions are your best friends for:
- 📊 Creating monthly reports
- 📅 Finding records from specific years
- 🎂 Tracking birthdays by month
- 📈 Analyzing trends by time periods

**Remember:** They're called DAY, MONTH, and YEAR - exactly what they do! 🎉
