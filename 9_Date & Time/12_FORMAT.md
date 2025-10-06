# Understanding SQL FORMAT Function - Complete Guide

## What is the FORMAT Function?

The **FORMAT** function in SQL helps you change how dates, times, and numbers **look**. It's like giving your data a makeover! The data stays the same, but it appears differently.

## Basic Syntax

```sql
FORMAT(value, format, [culture])
```

**Parameters:**
1. **value**: The date, time, or number you want to format
2. **format**: How you want it to look (using special codes)
3. **culture** (optional): Show it in a specific country's style (like Japan, France, USA)

---

## Simple Examples to Start

### Example 1: Basic Date Formatting

```sql
SELECT 
    '2025-01-15' AS OriginalDate,
    FORMAT(CAST('2025-01-15' AS DATE), 'dd/MM/yyyy') AS FormattedDate;
```

**Output:**
```
OriginalDate | FormattedDate
-------------|---------------
2025-01-15   | 15/01/2025
```

---

### Example 2: USA Date Format

```sql
SELECT 
    OrderID,
    OrderDate,
    FORMAT(OrderDate, 'MM-dd-yyyy') AS USA_Format
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15' AS DATE) AS OrderDate
    UNION ALL
    SELECT 2, CAST('2025-02-20' AS DATE)
    UNION ALL
    SELECT 3, CAST('2025-03-10' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | USA_Format
--------|------------|------------
1       | 2025-01-15 | 01-15-2025
2       | 2025-02-20 | 02-20-2025
3       | 2025-03-10 | 03-10-2025
```

---

### Example 3: European Date Format

```sql
SELECT 
    OrderID,
    OrderDate,
    FORMAT(OrderDate, 'dd-MM-yyyy') AS Europe_Format
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15' AS DATE) AS OrderDate
    UNION ALL
    SELECT 2, CAST('2025-02-20' AS DATE)
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | Europe_Format
--------|------------|---------------
1       | 2025-01-15 | 15-01-2025
2       | 2025-02-20 | 20-02-2025
```

---

## Understanding Format Specifiers for DAYS

Let's explore how different numbers of 'd' give different results:

```sql
SELECT 
    CAST('2025-01-15' AS DATE) AS OriginalDate,
    FORMAT(CAST('2025-01-15' AS DATE), 'dd') AS Two_D,
    FORMAT(CAST('2025-01-15' AS DATE), 'ddd') AS Three_D,
    FORMAT(CAST('2025-01-15' AS DATE), 'dddd') AS Four_D;
```

**Output:**
```
OriginalDate | Two_D | Three_D | Four_D
-------------|-------|---------|----------
2025-01-15   | 15    | Wed     | Wednesday
```

**What Each Does:**
- `dd` = Day number with leading zero (01, 02, 15)
- `ddd` = Short day name (Mon, Tue, Wed)
- `dddd` = Full day name (Monday, Tuesday, Wednesday)

---

## Understanding Format Specifiers for MONTHS

```sql
SELECT 
    CAST('2025-01-15' AS DATE) AS OriginalDate,
    FORMAT(CAST('2025-01-15' AS DATE), 'MM') AS Two_M,
    FORMAT(CAST('2025-01-15' AS DATE), 'MMM') AS Three_M,
    FORMAT(CAST('2025-01-15' AS DATE), 'MMMM') AS Four_M;
```

**Output:**
```
OriginalDate | Two_M | Three_M | Four_M
-------------|-------|---------|----------
2025-01-15   | 01    | Jan     | January
```

**What Each Does:**
- `MM` = Month number with leading zero (01, 02, 12)
- `MMM` = Short month name (Jan, Feb, Mar)
- `MMMM` = Full month name (January, February, March)

---

## Creating a Custom Complex Format

**Task**: Format date as "Day Wed Jan Q1 2025 02:30:45 PM"

Let's build this step by step:

```sql
SELECT 
    OrderID,
    CreationTime,
    -- Step 1: Static text "Day "
    'Day ' + 
    -- Step 2: Short day name (Wed)
    FORMAT(CreationTime, 'ddd ') +
    -- Step 3: Short month name (Jan)
    FORMAT(CreationTime, 'MMM ') +
    -- Step 4: Quarter (Q1)
    'Q' + DATENAME(QUARTER, CreationTime) + ' ' +
    -- Step 5: Year (2025)
    FORMAT(CreationTime, 'yyyy ') +
    -- Step 6: Time with AM/PM
    FORMAT(CreationTime, 'hh:mm:ss tt') AS CustomFormat
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15 14:30:45' AS DATETIME) AS CreationTime
    UNION ALL
    SELECT 2, CAST('2025-02-20 09:15:30' AS DATETIME)
    UNION ALL
    SELECT 3, CAST('2025-03-10 18:45:20' AS DATETIME)
) AS Orders;
```

**Output:**
```
OrderID | CreationTime        | CustomFormat
--------|---------------------|--------------------------------
1       | 2025-01-15 14:30:45 | Day Wed Jan Q1 2025 02:30:45 PM
2       | 2025-02-20 09:15:30 | Day Thu Feb Q1 2025 09:15:30 AM
3       | 2025-03-10 18:45:20 | Day Mon Mar Q1 2025 06:45:20 PM
```

---

## Common Format Specifier Reference

### Date Specifiers

| Code | Meaning | Example |
|------|---------|---------|
| `d` | Day (no leading zero) | 5 |
| `dd` | Day (with leading zero) | 05 |
| `ddd` | Short day name | Wed |
| `dddd` | Full day name | Wednesday |
| `M` | Month (no leading zero) | 8 |
| `MM` | Month (with leading zero) | 08 |
| `MMM` | Short month name | Aug |
| `MMMM` | Full month name | August |
| `yy` | 2-digit year | 25 |
| `yyyy` | 4-digit year | 2025 |

### Time Specifiers

| Code | Meaning | Example |
|------|---------|---------|
| `h` | Hour 12-hour (no leading zero) | 2 |
| `hh` | Hour 12-hour (with leading zero) | 02 |
| `H` | Hour 24-hour (no leading zero) | 14 |
| `HH` | Hour 24-hour (with leading zero) | 14 |
| `m` | Minute (no leading zero) | 5 |
| `mm` | Minute (with leading zero) | 05 |
| `s` | Second (no leading zero) | 7 |
| `ss` | Second (with leading zero) | 07 |
| `tt` | AM/PM | PM |

---

## Using FORMAT for Data Aggregation

Instead of using DATEPART or DATENAME, you can use FORMAT to create custom date groupings!

### Example: Sales Report by Month

```sql
SELECT 
    FORMAT(OrderDate, 'MMM yy') AS OrderMonth,
    COUNT(*) AS NumberOfOrders
FROM (
    SELECT CAST('2025-01-05' AS DATE) AS OrderDate
    UNION ALL SELECT CAST('2025-01-15' AS DATE)
    UNION ALL SELECT CAST('2025-01-22' AS DATE)
    UNION ALL SELECT CAST('2025-01-28' AS DATE)
    UNION ALL SELECT CAST('2025-02-03' AS DATE)
    UNION ALL SELECT CAST('2025-02-10' AS DATE)
    UNION ALL SELECT CAST('2025-02-18' AS DATE)
    UNION ALL SELECT CAST('2025-02-25' AS DATE)
    UNION ALL SELECT CAST('2025-03-07' AS DATE)
    UNION ALL SELECT CAST('2025-03-20' AS DATE)
) AS Orders
GROUP BY FORMAT(OrderDate, 'MMM yy')
ORDER BY MIN(OrderDate);
```

**Output:**
```
OrderMonth | NumberOfOrders
-----------|---------------
Jan 25     | 4
Feb 25     | 4
Mar 25     | 2
```

Much better than showing "1, 2, 3" for months!

---

## Formatting Numbers

FORMAT works with numbers too!

### Example: Number Formatting

```sql
SELECT 
    1234567.89 AS OriginalNumber,
    FORMAT(1234567.89, 'N') AS WithCommas,
    FORMAT(1234567.89, 'N0') AS NoDecimals,
    FORMAT(1234567.89, 'N2') AS TwoDecimals,
    FORMAT(1234567.89, 'C') AS Currency,
    FORMAT(1234567.89, 'C0') AS CurrencyNoDecimals,
    FORMAT(0.75, 'P') AS Percentage,
    FORMAT(0.75, 'P0') AS PercentageNoDecimals;
```

**Output:**
```
OriginalNumber | WithCommas   | NoDecimals | TwoDecimals  | Currency      | CurrencyNoDecimals | Percentage | PercentageNoDecimals
---------------|--------------|------------|--------------|---------------|--------------------|-----------|--------------------- 
1234567.89     | 1,234,567.89 | 1,234,568  | 1,234,567.89 | $1,234,567.89 | $1,234,568         | 75.00%    | 75%
```

**Number Format Codes:**
- `N` = Number with commas
- `C` = Currency (adds $ sign)
- `P` = Percentage (multiplies by 100, adds %)
- `D` = Decimal (integers only)

---

## Real-World Use Case: Data Standardization

Imagine you receive data from different sources with different date formats:

```sql
-- Different source data
SELECT 
    'CSV File' AS Source,
    '01/15/2025' AS DateReceived
UNION ALL
SELECT 'API Call', '2025-01-15'
UNION ALL
SELECT 'Database', '15-Jan-2025';

-- Standardize to one format
SELECT 
    Source,
    DateReceived,
    FORMAT(
        CASE 
            WHEN Source = 'CSV File' THEN CAST(DateReceived AS DATE)
            WHEN Source = 'API Call' THEN CAST(DateReceived AS DATE)
            WHEN Source = 'Database' THEN CAST(DateReceived AS DATE)
        END, 
        'yyyy-MM-dd'
    ) AS StandardFormat
FROM (
    SELECT 'CSV File' AS Source, CAST('2025-01-15' AS DATE) AS DateReceived
    UNION ALL SELECT 'API Call', CAST('2025-01-15' AS DATE)
    UNION ALL SELECT 'Database', CAST('2025-01-15' AS DATE)
) AS DataSources;
```

**Output:**
```
Source     | DateReceived | StandardFormat
-----------|--------------|---------------
CSV File   | 2025-01-15   | 2025-01-15
API Call   | 2025-01-15   | 2025-01-15
Database   | 2025-01-15   | 2025-01-15
```

Now all dates are in one standard format!

---

## Complete Examples Collection

### Example: Various Date Formats

```sql
SELECT 
    GETDATE() AS CurrentDateTime,
    FORMAT(GETDATE(), 'dd/MM/yyyy') AS Format1,
    FORMAT(GETDATE(), 'MM-dd-yyyy') AS Format2,
    FORMAT(GETDATE(), 'yyyy.MM.dd') AS Format3,
    FORMAT(GETDATE(), 'MMMM dd, yyyy') AS Format4,
    FORMAT(GETDATE(), 'dddd, MMMM dd, yyyy') AS Format5,
    FORMAT(GETDATE(), 'MMM dd yy') AS Format6,
    FORMAT(GETDATE(), 'dd-MMM-yyyy') AS Format7;
```

**Sample Output (if today is August 20, 2025):**
```
CurrentDateTime     | Format1    | Format2    | Format3    | Format4          | Format5                    | Format6    | Format7
--------------------|------------|------------|------------|------------------|----------------------------|------------|--------------
2025-08-20 14:35:55 | 20/08/2025 | 08-20-2025 | 2025.08.20 | August 20, 2025  | Wednesday, August 20, 2025 | Aug 20 25  | 20-Aug-2025
```

---

## Using Culture Parameter (Optional)

```sql
SELECT 
    1234567.89 AS Number,
    FORMAT(1234567.89, 'C', 'en-US') AS USA_Currency,
    FORMAT(1234567.89, 'C', 'fr-FR') AS France_Currency,
    FORMAT(1234567.89, 'C', 'ja-JP') AS Japan_Currency;
```

**Output:**
```
Number     | USA_Currency  | France_Currency | Japan_Currency
-----------|---------------|-----------------|----------------
1234567.89 | $1,234,567.89 | 1 234 567,89 €  | ¥1,234,568
```

Different countries format numbers differently!

---

## Key Points to Remember

1. FORMAT changes how data **looks**, not what it **is**
2. Format codes are **case-sensitive** (MM ≠ mm)
3. Use FORMAT for creating **readable reports**
4. Great for **standardizing data** from different sources
5. Works with both **dates and numbers**
6. Culture parameter is **optional** (defaults to US)
7. More flexible than CONVERT for custom formats

---

## Quick Reference Card

```
Popular Date Formats:
'yyyy-MM-dd'           → 2025-08-20
'MM/dd/yyyy'           → 08/20/2025
'dd/MM/yyyy'           → 20/08/2025
'MMMM dd, yyyy'        → August 20, 2025
'MMM dd yy'            → Aug 20 25
'dddd, MMMM dd, yyyy'  → Wednesday, August 20, 2025

Popular Number Formats:
'N'  → 1,234,567.89 (with commas)
'C'  → $1,234,567.89 (currency)
'P'  → 75.00% (percentage)
'D'  → 123 (decimal, integers only)
```

---

## Practice Exercise

Try creating a custom report format:

```sql
SELECT 
    OrderID,
    OrderDate,
    OrderAmount,
    'Order #' + CAST(OrderID AS VARCHAR) + 
    ' placed on ' + FORMAT(OrderDate, 'dddd, MMMM dd, yyyy') +
    ' for ' + FORMAT(OrderAmount, 'C') AS OrderSummary
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15' AS DATE) AS OrderDate, 1234.50 AS OrderAmount
) AS Orders;
```

Now you're a FORMAT function expert!
