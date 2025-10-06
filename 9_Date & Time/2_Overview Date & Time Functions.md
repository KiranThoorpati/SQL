# How to Work with Dates in SQL - Complete Guide

## Introduction

Now that we know what dates and times are, let's learn **how to manipulate them** using SQL functions!

Let's take our example date: **August 20, 2025** (written as `2025-08-20`)

---

## Four Ways to Work with Dates

### 1. 📤 Extract Parts (Pull out specific pieces)
### 2. 🎨 Change Format (Make dates look different)
### 3. ➕➖ Do Calculations (Add, subtract, find differences)
### 4. ✅ Validate (Check if it's a real date)

---

## Category 1: Extracting Date Parts (7 Functions)

Pull out individual pieces from a date.

### Example: Breaking Apart a Date

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    YEAR('2025-08-20') AS YearOnly,
    MONTH('2025-08-20') AS MonthOnly,
    DAY('2025-08-20') AS DayOnly;
```

**Output:**
```
OriginalDate  YearOnly  MonthOnly  DayOnly
------------  --------  ---------  -------
2025-08-20    2025      8          20
```

**Real-life use:** Want to see all orders from August? Just extract the month!

---

## Category 2: Changing Date Format (3 Functions)

Make dates look different - change how they're displayed.

### Example: Different Date Formats

```sql
SELECT 
    CONVERT(VARCHAR, '2025-08-20', 101) AS USA_Format,
    CONVERT(VARCHAR, '2025-08-20', 103) AS UK_Format,
    FORMAT(CAST('2025-08-20' AS DATE), 'MM/dd/yy') AS Short_Format,
    FORMAT(CAST('2025-08-20' AS DATE), 'MMMM dd, yyyy') AS Long_Format;
```

**Output:**
```
USA_Format   UK_Format    Short_Format  Long_Format
----------   ----------   ------------  -------------------
08/20/2025   20/08/2025   08/20/25      August 20, 2025
```

**Real-life use:** Create reports that look good for different countries or make dates easy to read.

---

## Category 3: Date Calculations (2 Functions)

Add time, subtract time, or find differences between dates.

### Example 1: Adding Time to a Date

```sql
SELECT 
    '2025-08-20' AS StartDate,
    DATEADD(YEAR, 3, '2025-08-20') AS Add3Years,
    DATEADD(MONTH, 2, '2025-08-20') AS Add2Months,
    DATEADD(DAY, 10, '2025-08-20') AS Add10Days;
```

**Output:**
```
StartDate    Add3Years    Add2Months   Add10Days
----------   ----------   ----------   ----------
2025-08-20   2028-08-20   2025-10-20   2025-08-30
```

### Example 2: Finding Differences Between Dates

```sql
SELECT 
    '2025-08-20' AS Date1,
    '2025-09-20' AS Date2,
    DATEDIFF(DAY, '2025-08-20', '2025-09-20') AS DaysBetween,
    DATEDIFF(MONTH, '2025-08-20', '2025-09-20') AS MonthsBetween;
```

**Output:**
```
Date1        Date2        DaysBetween  MonthsBetween
----------   ----------   -----------  -------------
2025-08-20   2025-09-20   31           1
```

**Real-life use:** Calculate how many days until your birthday or how long until a project deadline!

---

## Category 4: Validating Dates (1 Function)

Check if something is actually a valid date.

### Example: Testing Different Values

```sql
SELECT 
    ISDATE('2025-08-20') AS ValidDate,
    ISDATE('2025-13-45') AS InvalidDate,
    ISDATE('Hello World') AS NotADate,
    ISDATE('2025') AS JustYear;
```

**Output:**
```
ValidDate  InvalidDate  NotADate  JustYear
---------  -----------  --------  --------
1          0            0         1
```

**What the numbers mean:**
- **1** = TRUE (Yes, it's a valid date!)
- **0** = FALSE (No, it's not a valid date)

**Real-life use:** Clean up messy data before analyzing it!

---

## Complete Example: Student Project Tracker

Let's combine all four categories in one example!

```sql
SELECT 
    'Science Project' AS ProjectName,
    '2025-09-01' AS StartDate,
    
    -- EXTRACT: Get parts
    MONTH('2025-09-01') AS StartMonth,
    
    -- FORMAT: Make it look nice
    FORMAT(CAST('2025-09-01' AS DATE), 'MMMM dd, yyyy') AS FormattedStart,
    
    -- CALCULATE: Find deadline (add 30 days)
    DATEADD(DAY, 30, '2025-09-01') AS Deadline,
    
    -- CALCULATE: Days remaining
    DATEDIFF(DAY, GETDATE(), DATEADD(DAY, 30, '2025-09-01')) AS DaysLeft,
    
    -- VALIDATE: Is the date valid?
    ISDATE('2025-09-01') AS IsValidDate;
```

**Output:**
```
ProjectName      StartDate   StartMonth  FormattedStart        Deadline    DaysLeft  IsValidDate
--------------   ----------  ----------  -------------------   ----------  --------  -----------
Science Project  2025-09-01  9           September 01, 2025    2025-10-01  25        1
```

---

## Summary: 13 SQL Date Functions in 4 Groups

| Category | Functions | What They Do |
|----------|-----------|--------------|
| **1. Extract Parts** | YEAR, MONTH, DAY, DATEPART, DATENAME (7 functions) | Pull out year, month, day, etc. |
| **2. Change Format** | FORMAT, CONVERT, CAST (3 functions) | Make dates look different |
| **3. Calculations** | DATEADD, DATEDIFF (2 functions) | Add/subtract time, find differences |
| **4. Validate** | ISDATE (1 function) | Check if it's a real date |

---

## Quick Reference Chart

| I Want To... | Use This Function | Example |
|--------------|-------------------|---------|
| Get just the year | YEAR() | YEAR('2025-08-20') → 2025 |
| Get just the month | MONTH() | MONTH('2025-08-20') → 8 |
| Make date look pretty | FORMAT() | FORMAT(date, 'MMMM dd, yyyy') |
| Add 5 days | DATEADD() | DATEADD(DAY, 5, date) |
| Find days between dates | DATEDIFF() | DATEDIFF(DAY, date1, date2) |
| Check if valid | ISDATE() | ISDATE('2025-08-20') → 1 |

---

## Why Learn These Functions?

✅ **Organize data** - Group sales by month or year  
✅ **Create reports** - Make dates look professional  
✅ **Calculate deadlines** - Find due dates automatically  
✅ **Clean data** - Remove invalid dates  
✅ **Analyze trends** - See patterns over time  

---

## Practice Challenge! 🎯

Try this yourself:
1. Take your birthday
2. Extract the year, month, and day
3. Calculate how many days until your next birthday
4. Format it to look nice

You now have all the tools to become a date master in SQL! 🎉
