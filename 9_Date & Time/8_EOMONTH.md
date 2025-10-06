# Understanding SQL EOMONTH Function

## What is EOMONTH?

**EOMONTH** stands for **End Of Month**. This function finds the **last day** of any month. It's super simple and very useful!

## How Does EOMONTH Work?

The function takes any date and returns the **last day of that month**.

### Visual Examples:

```
Input Date: 2025-08-20  →  EOMONTH returns: 2025-08-31
Input Date: 2025-02-01  →  EOMONTH returns: 2025-02-28
Input Date: 2025-03-31  →  EOMONTH returns: 2025-03-31 (already last day!)
```

**Key Point**: It only changes the **day** part to the last day of that month. Year and month stay the same!

---

## Basic Syntax

```sql
EOMONTH(date)
```

- **date**: The date you want to find the end of month for

Very simple - just one parameter!

---

## Examples with Expected Outputs

### Example 1: Find End of Month for a Single Date

```sql
SELECT EOMONTH('2025-08-20') AS LastDayOfMonth;
```

**Output:**
```
LastDayOfMonth
--------------
2025-08-31
```

August has 31 days, so it returns August 31st!

---

### Example 2: February Month (Shorter Month)

```sql
SELECT EOMONTH('2025-02-01') AS LastDayOfFebruary;
```

**Output:**
```
LastDayOfFebruary
-----------------
2025-02-28
```

February 2025 has only 28 days, so it returns February 28th!

---

### Example 3: Already at End of Month

```sql
SELECT EOMONTH('2025-03-31') AS LastDayOfMarch;
```

**Output:**
```
LastDayOfMarch
--------------
2025-03-31
```

The date is already the last day, so nothing changes!

---

### Example 4: Working with a Table

Let's say we have a **SalesOrders** table:

```sql
OrderID | CreationTime
--------|-------------
1       | 2025-01-05
2       | 2025-01-15
3       | 2025-01-22
4       | 2025-01-28
5       | 2025-02-03
6       | 2025-02-10
7       | 2025-02-18
8       | 2025-02-25
9       | 2025-03-07
10      | 2025-03-20
```

Now let's find the end of month for each order:

```sql
SELECT 
    OrderID,
    CreationTime,
    EOMONTH(CreationTime) AS EndOfMonth
FROM SalesOrders;
```

**Output:**
```
OrderID | CreationTime | EndOfMonth
--------|--------------|------------
1       | 2025-01-05   | 2025-01-31
2       | 2025-01-15   | 2025-01-31
3       | 2025-01-22   | 2025-01-31
4       | 2025-01-28   | 2025-01-31
5       | 2025-02-03   | 2025-02-28
6       | 2025-02-10   | 2025-02-28
7       | 2025-02-18   | 2025-02-28
8       | 2025-02-25   | 2025-02-28
9       | 2025-03-07   | 2025-03-31
10      | 2025-03-20   | 2025-03-31
```

All January dates show January 31st, all February dates show February 28th, and so on!

---

## Bonus: Finding the START of Month (First Day)

**Question**: Is there a function to get the **first day** of the month?

**Answer**: No direct function, but we can use a trick with **DATETRUNC**!

### Method: Using DATETRUNC

```sql
SELECT 
    OrderID,
    CreationTime,
    DATETRUNC(MONTH, CreationTime) AS StartOfMonth,
    EOMONTH(CreationTime) AS EndOfMonth
FROM SalesOrders;
```

**Output:**
```
OrderID | CreationTime | StartOfMonth        | EndOfMonth
--------|--------------|---------------------|------------
1       | 2025-01-05   | 2025-01-01 00:00:00 | 2025-01-31
2       | 2025-01-15   | 2025-01-01 00:00:00 | 2025-01-31
3       | 2025-01-22   | 2025-01-01 00:00:00 | 2025-01-31
4       | 2025-01-28   | 2025-01-01 00:00:00 | 2025-01-31
5       | 2025-02-03   | 2025-02-01 00:00:00 | 2025-02-28
```

**Problem**: StartOfMonth shows time (00:00:00) because DATETRUNC returns datetime!

---

### Better Solution: Remove the Time Using CAST

```sql
SELECT 
    OrderID,
    CreationTime,
    CAST(DATETRUNC(MONTH, CreationTime) AS DATE) AS StartOfMonth,
    EOMONTH(CreationTime) AS EndOfMonth
FROM SalesOrders;
```

**Output:**
```
OrderID | CreationTime | StartOfMonth | EndOfMonth
--------|--------------|--------------|------------
1       | 2025-01-05   | 2025-01-01   | 2025-01-31
2       | 2025-01-15   | 2025-01-01   | 2025-01-31
3       | 2025-01-22   | 2025-01-01   | 2025-01-31
4       | 2025-01-28   | 2025-01-01   | 2025-01-31
5       | 2025-02-03   | 2025-02-01   | 2025-02-28
6       | 2025-02-10   | 2025-02-01   | 2025-02-28
7       | 2025-02-18   | 2025-02-01   | 2025-02-28
8       | 2025-02-25   | 2025-02-01   | 2025-02-28
9       | 2025-03-07   | 2025-03-01   | 2025-03-31
10      | 2025-03-20   | 2025-03-01   | 2025-03-31
```

**Perfect!** Now we have both the start and end of each month in clean date format!

---

## Complete Example: Start and End of Month

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    CAST(DATETRUNC(MONTH, '2025-08-20') AS DATE) AS FirstDayOfMonth,
    EOMONTH('2025-08-20') AS LastDayOfMonth;
```

**Output:**
```
OriginalDate | FirstDayOfMonth | LastDayOfMonth
-------------|-----------------|---------------
2025-08-20   | 2025-08-01      | 2025-08-31
```

---

## Real-World Use Case: Monthly Report Dates

Imagine you're creating a monthly sales report and need to show the report period:

```sql
SELECT 
    OrderID,
    'Month: ' + 
    CAST(CAST(DATETRUNC(MONTH, CreationTime) AS DATE) AS VARCHAR) + 
    ' to ' + 
    CAST(EOMONTH(CreationTime) AS VARCHAR) AS ReportPeriod,
    CreationTime
FROM SalesOrders;
```

**Output:**
```
OrderID | ReportPeriod                  | CreationTime
--------|-------------------------------|-------------
1       | Month: 2025-01-01 to 2025-01-31 | 2025-01-05
5       | Month: 2025-02-01 to 2025-02-28 | 2025-02-03
9       | Month: 2025-03-01 to 2025-03-31 | 2025-03-07
```

---

## Comparing Different Months

```sql
SELECT 
    'January 2025' AS Month,
    EOMONTH('2025-01-01') AS LastDay
UNION ALL
SELECT 
    'February 2025',
    EOMONTH('2025-02-01')
UNION ALL
SELECT 
    'March 2025',
    EOMONTH('2025-03-01')
UNION ALL
SELECT 
    'April 2025',
    EOMONTH('2025-04-01');
```

**Output:**
```
Month          | LastDay
---------------|------------
January 2025   | 2025-01-31
February 2025  | 2025-02-28
March 2025     | 2025-03-31
April 2025     | 2025-04-30
```

Different months have different numbers of days!

---

## Key Points to Remember

1. **EOMONTH** finds the **last day** of any month
2. It only accepts **one parameter** (the date)
3. Output is always a **date** (no time component)
4. Works with **any date** - beginning, middle, or end of month
5. Very useful for **monthly reports** and analysis
6. To get **first day of month**: Use `CAST(DATETRUNC(MONTH, date) AS DATE)`

---

## Quick Reference

| Function | What It Does | Example Output |
|----------|--------------|----------------|
| `EOMONTH('2025-08-20')` | Last day of month | 2025-08-31 |
| `CAST(DATETRUNC(MONTH, '2025-08-20') AS DATE)` | First day of month | 2025-08-01 |

---

## Practice Exercise

Try finding the start and end of the current month:

```sql
SELECT 
    GETDATE() AS Today,
    CAST(DATETRUNC(MONTH, GETDATE()) AS DATE) AS MonthStart,
    EOMONTH(GETDATE()) AS MonthEnd;
```

This will show you today's date along with the first and last day of the current month!
