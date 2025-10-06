# SQL Date & Time Functions - Complete Summary

## What We've Learned

We've covered **13 powerful date and time functions** in SQL! Let's review them with simple examples.

---

## Part 1: Extracting Date Parts (7 Functions)

These functions help you pull out specific pieces from a date.

### Example: Breaking Down a Date

```sql
SELECT 
    YEAR('2025-08-15') AS YearPart,
    MONTH('2025-08-15') AS MonthPart,
    DAY('2025-08-15') AS DayPart,
    DATEPART(QUARTER, '2025-08-15') AS QuarterPart,
    DATEPART(WEEK, '2025-08-15') AS WeekPart,
    DATEPART(WEEKDAY, '2025-08-15') AS WeekdayPart,
    DATENAME(MONTH, '2025-08-15') AS MonthName;
```

**Output:**
```
YearPart  MonthPart  DayPart  QuarterPart  WeekPart  WeekdayPart  MonthName
--------  ---------  -------  -----------  --------  -----------  ---------
2025      8          15       3            33        6            August
```

**When to use these?**
- Creating monthly sales reports
- Filtering data by year or quarter
- Grouping orders by day of the week

---

## Part 2: Changing Date Formats

These functions help you convert dates to different formats or data types.

### Example: Converting Date Formats

```sql
SELECT 
    CONVERT(VARCHAR, GETDATE(), 101) AS US_Format,
    CONVERT(VARCHAR, GETDATE(), 103) AS UK_Format,
    CAST('2025-08-15' AS DATE) AS ConvertedDate,
    FORMAT(GETDATE(), 'dd-MMM-yyyy') AS CustomFormat;
```

**Output:**
```
US_Format    UK_Format    ConvertedDate  CustomFormat
-----------  -----------  -------------  -------------
10/07/2025   07/10/2025   2025-08-15     07-Oct-2025
```

**When to use these?**
- Creating reports for different countries
- Converting text to actual dates
- Making dates look pretty in reports

---

## Part 3: Date Math Operations

These functions let you add, subtract, and calculate differences between dates.

### Example 1: Adding Time to Dates

```sql
SELECT 
    DATEADD(DAY, 7, '2025-01-01') AS Add7Days,
    DATEADD(MONTH, 3, '2025-01-01') AS Add3Months,
    DATEADD(YEAR, 1, '2025-01-01') AS Add1Year;
```

**Output:**
```
Add7Days     Add3Months   Add1Year
-----------  -----------  ----------
2025-01-08   2025-04-01   2026-01-01
```

### Example 2: Finding Differences Between Dates

```sql
SELECT 
    DATEDIFF(DAY, '2025-01-01', '2025-01-15') AS DaysDifference,
    DATEDIFF(MONTH, '2025-01-01', '2025-06-01') AS MonthsDifference,
    DATEDIFF(YEAR, '2020-01-01', '2025-01-01') AS YearsDifference;
```

**Output:**
```
DaysDifference  MonthsDifference  YearsDifference
--------------  ----------------  ---------------
14              5                 5
```

**When to use these?**
- Calculating delivery dates
- Finding how many days until a deadline
- Calculating someone's age
- Finding order processing time

---

## Part 4: Validating Dates

This function checks if a value is a valid date.

### Example: Checking Valid Dates

```sql
SELECT 
    ISDATE('2025-08-15') AS ValidDate,
    ISDATE('2025-13-40') AS InvalidDate,
    ISDATE('Hello') AS NotADate;
```

**Output:**
```
ValidDate  InvalidDate  NotADate
---------  -----------  --------
1          0            0
```

**When to use this?**
- Cleaning messy data
- Finding errors in date columns
- Preventing conversion errors

---

## Real-World Example: Student Attendance Report

Let's combine everything we learned!

```sql
-- Sample student attendance data
SELECT 
    StudentName,
    AttendanceDate,
    DATENAME(WEEKDAY, AttendanceDate) AS DayOfWeek,
    DATEPART(MONTH, AttendanceDate) AS MonthNumber,
    DATENAME(MONTH, AttendanceDate) AS MonthName,
    DATEDIFF(DAY, AttendanceDate, GETDATE()) AS DaysAgo
FROM (
    VALUES 
        ('Ravi', '2025-10-01'),
        ('Priya', '2025-10-02'),
        ('Amit', '2025-10-03')
) AS Attendance(StudentName, AttendanceDate);
```

**Output:**
```
StudentName  AttendanceDate  DayOfWeek  MonthNumber  MonthName  DaysAgo
-----------  --------------  ---------  -----------  ---------  -------
Ravi         2025-10-01      Wednesday  10           October    6
Priya        2025-10-02      Thursday   10           October    5
Amit         2025-10-03      Friday     10           October    4
```

---

## Why Date Functions are Amazing

1. **Data Analysis** - Group sales by month, find trends by quarter
2. **Reporting** - Create beautiful, formatted date displays
3. **Calculations** - Find ages, calculate durations, project future dates
4. **Data Cleaning** - Validate dates, find errors, fix bad data
5. **Filtering** - Find records from last week, this year, specific quarters

---

## Quick Reference Chart

| Task | Function to Use | Example |
|------|----------------|---------|
| Get year from date | YEAR() | YEAR('2025-08-15') → 2025 |
| Add days to date | DATEADD() | DATEADD(DAY, 5, '2025-01-01') |
| Find days between dates | DATEDIFF() | DATEDIFF(DAY, date1, date2) |
| Check if valid date | ISDATE() | ISDATE('2025-08-15') → 1 |
| Get month name | DATENAME() | DATENAME(MONTH, date) |
| Change date format | CONVERT() | CONVERT(VARCHAR, date, 103) |

---

## What's Next?

Next, we'll learn about **NULL functions** - special tools to handle missing or empty values in your database tables!

---

**Remember:** Practice makes perfect! Try creating your own examples with your birthday, school events, or favorite holidays to master these functions! 🎉
