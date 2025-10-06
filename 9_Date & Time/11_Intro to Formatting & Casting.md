# Understanding SQL Date Formats, Formatting & Casting

## What are Date Formats?

A **date format** is like a pattern that shows how dates are written. Just like different countries write addresses differently, different places write dates differently!

### Breaking Down a Date Format

Let's look at this date: **2025-08-20 14:35:55**

This date is made of **numbers** and **special characters**:
- Numbers: 2025, 08, 20, 14, 35, 55
- Characters: `-` (minus), `:` (colon), space

### Format Specifiers (The Secret Code!)

We use special letters to represent each part:

| Part | Format Code | Example |
|------|-------------|---------|
| Year (4 digits) | `yyyy` | 2025 |
| Month (2 digits) | `MM` | 08 |
| Day (2 digits) | `dd` | 20 |
| Hour (24-hour) | `HH` | 14 |
| Minute | `mm` | 35 |
| Second | `ss` | 55 |

**Complete Format Code**: `yyyy-MM-dd HH:mm:ss`

**⚠️ IMPORTANT**: Format codes are **case-sensitive**!
- `MM` = Month (big M)
- `mm` = Minute (small m)

---

## Different Date Formats Around the World

Different countries write dates differently:

### 1. International Standard (ISO 8601) - Used by SQL
```
Format: yyyy-MM-dd
Example: 2025-08-20
(Year first, then Month, then Day)
```

### 2. USA Format
```
Format: MM/dd/yyyy
Example: 08/20/2025
(Month first, then Day, then Year)
```

### 3. European Format
```
Format: dd/MM/yyyy
Example: 20/08/2025
(Day first, then Month, then Year)
```

---

## What is Formatting?

**Formatting** means changing **how data looks** without changing what it actually is!

Think of it like changing someone's outfit - the person is still the same, just dressed differently! 👕➡️👔

---

## Formatting Dates with FORMAT Function

The `FORMAT()` function lets you display dates in different styles.

### Example 1: Change to USA Format

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    FORMAT(CAST('2025-08-20' AS DATE), 'MM/dd/yyyy') AS USA_Format;
```

**Output:**
```
OriginalDate | USA_Format
-------------|------------
2025-08-20   | 08/20/2025
```

---

### Example 2: Show Only Month and Year

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    FORMAT(CAST('2025-08-20' AS DATE), 'MMM yyyy') AS Month_Year;
```

**Output:**
```
OriginalDate | Month_Year
-------------|------------
2025-08-20   | Aug 2025
```

**Note**: `MMM` gives a 3-letter abbreviation (Jan, Feb, Mar...)

---

### Example 3: Show Full Month Name

```sql
SELECT 
    '2025-08-20' AS OriginalDate,
    FORMAT(CAST('2025-08-20' AS DATE), 'MMMM yyyy') AS Full_Month_Year;
```

**Output:**
```
OriginalDate | Full_Month_Year
-------------|----------------
2025-08-20   | August 2025
```

**Note**: `MMMM` (4 M's) gives the full month name!

---

### Example 4: Different Date Patterns

```sql
SELECT 
    GETDATE() AS CurrentDateTime,
    FORMAT(GETDATE(), 'dd/MM/yyyy') AS European_Style,
    FORMAT(GETDATE(), 'MM-dd-yyyy') AS USA_Style,
    FORMAT(GETDATE(), 'yyyy.MM.dd') AS Dot_Separated,
    FORMAT(GETDATE(), 'dd MMM yyyy') AS Day_Month_Year,
    FORMAT(GETDATE(), 'MMMM dd, yyyy') AS Full_Format;
```

**Output (if today is August 20, 2025):**
```
CurrentDateTime     | European_Style | USA_Style  | Dot_Separated | Day_Month_Year | Full_Format
--------------------|----------------|------------|---------------|----------------|------------------
2025-08-20 14:35:55 | 20/08/2025     | 08-20-2025 | 2025.08.20    | 20 Aug 2025    | August 20, 2025
```

---

## Formatting Dates with CONVERT Function (Style Numbers)

SQL Server has another way - using **style numbers**!

### Example 5: Using CONVERT with Style Numbers

```sql
SELECT 
    GETDATE() AS CurrentDateTime,
    CONVERT(VARCHAR, GETDATE(), 1) AS Style_1,
    CONVERT(VARCHAR, GETDATE(), 3) AS Style_3,
    CONVERT(VARCHAR, GETDATE(), 6) AS Style_6,
    CONVERT(VARCHAR, GETDATE(), 112) AS Style_112;
```

**Output:**
```
CurrentDateTime     | Style_1    | Style_3    | Style_6      | Style_112
--------------------|------------|------------|--------------|----------
2025-08-20 14:35:55 | 08/20/25   | 20/08/25   | 20 Aug 25    | 20250820
```

### Popular Style Numbers

| Style | Format | Example |
|-------|--------|---------|
| 1 | MM/DD/YY | 08/20/25 |
| 3 | DD/MM/YY | 20/08/25 |
| 6 | DD Mon YY | 20 Aug 25 |
| 101 | MM/DD/YYYY | 08/20/2025 |
| 103 | DD/MM/YYYY | 20/08/2025 |
| 112 | YYYYMMDD | 20250820 |

---

## Formatting Numbers

You can also format numbers, not just dates!

### Example 6: Number Formatting

```sql
SELECT 
    1234567.89 AS OriginalNumber,
    FORMAT(1234567.89, 'N') AS Numeric_Format,
    FORMAT(1234567.89, 'C') AS Currency_Format,
    FORMAT(0.75, 'P') AS Percentage_Format;
```

**Output:**
```
OriginalNumber | Numeric_Format | Currency_Format | Percentage_Format
---------------|----------------|-----------------|------------------
1234567.89     | 1,234,567.89   | $1,234,567.89   | 75.00%
```

**What Each Does:**
- `N` = Adds commas for thousands
- `C` = Shows as currency with $ sign
- `P` = Shows as percentage with % sign

---

## What is Casting?

**Casting** means changing the **data type** of a value!

Think of it like transforming a toy car into a real car - it becomes something different! 🚗➡️🚙

### Common Data Type Changes:
- String → Number
- Number → String
- String → Date
- Date → String

---

## Casting with CAST Function

### Example 7: String to Integer

```sql
SELECT 
    '123' AS String_Value,
    CAST('123' AS INT) AS Integer_Value,
    CAST('123' AS INT) + 100 AS Math_Result;
```

**Output:**
```
String_Value | Integer_Value | Math_Result
-------------|---------------|------------
123          | 123           | 223
```

Now we can do math with it!

---

### Example 8: Date to String

```sql
SELECT 
    CAST('2025-08-20' AS DATE) AS Date_Type,
    CAST(CAST('2025-08-20' AS DATE) AS VARCHAR) AS String_Type;
```

**Output:**
```
Date_Type  | String_Type
-----------|------------
2025-08-20 | 2025-08-20
```

---

### Example 9: String to Date

```sql
SELECT 
    '2025-08-20' AS String_Value,
    CAST('2025-08-20' AS DATE) AS Date_Value,
    YEAR(CAST('2025-08-20' AS DATE)) AS Extract_Year;
```

**Output:**
```
String_Value | Date_Value | Extract_Year
-------------|------------|-------------
2025-08-20   | 2025-08-20 | 2025
```

Once it's a date, we can extract parts from it!

---

### Example 10: DateTime to Date (Remove Time)

```sql
SELECT 
    GETDATE() AS With_Time,
    CAST(GETDATE() AS DATE) AS Only_Date;
```

**Output:**
```
With_Time           | Only_Date
--------------------|------------
2025-08-20 14:35:55 | 2025-08-20
```

Perfect for removing time information!

---

## Casting with CONVERT Function

`CONVERT` works like `CAST` but with extra options!

### Example 11: Using CONVERT

```sql
SELECT 
    '123' AS Original,
    CONVERT(INT, '123') AS To_Integer,
    CONVERT(DATE, '2025-08-20') AS To_Date,
    CONVERT(VARCHAR, 12345) AS To_String;
```

**Output:**
```
Original | To_Integer | To_Date    | To_String
---------|------------|------------|----------
123      | 123        | 2025-08-20 | 12345
```

---

## Real-World Example: Formatting Order Report

```sql
-- Sample Orders Table
SELECT 
    OrderID,
    OrderDate,
    OrderAmount,
    
    -- Different Date Formats
    FORMAT(OrderDate, 'MMMM dd, yyyy') AS Formatted_Date,
    CONVERT(VARCHAR, OrderDate, 101) AS USA_Date,
    
    -- Number Formatting
    FORMAT(OrderAmount, 'C') AS Amount_Currency,
    FORMAT(OrderAmount, 'N2') AS Amount_Number
    
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-15' AS DATE) AS OrderDate, 1234.50 AS OrderAmount
    UNION ALL
    SELECT 2, CAST('2025-02-20' AS DATE), 5678.99
    UNION ALL
    SELECT 3, CAST('2025-03-10' AS DATE), 999.00
) AS Orders;
```

**Output:**
```
OrderID | OrderDate  | OrderAmount | Formatted_Date    | USA_Date   | Amount_Currency | Amount_Number
--------|------------|-------------|-------------------|------------|-----------------|---------------
1       | 2025-01-15 | 1234.50     | January 15, 2025  | 01/15/2025 | $1,234.50       | 1,234.50
2       | 2025-02-20 | 5678.99     | February 20, 2025 | 02/20/2025 | $5,678.99       | 5,678.99
3       | 2025-03-10 | 999.00      | March 10, 2025    | 03/10/2025 | $999.00         | 999.00
```

---

## Key Differences: Formatting vs Casting

### Formatting
✅ Changes how data **looks**  
✅ Data type stays the same  
✅ Used for display purposes  
✅ Uses: `FORMAT()`, `CONVERT()` with styles

### Casting
✅ Changes the **data type**  
✅ Converts data from one type to another  
✅ Used for calculations and operations  
✅ Uses: `CAST()`, `CONVERT()`

---

## Quick Reference Guide

### Common Format Codes
```
yyyy = 4-digit year (2025)
yy   = 2-digit year (25)
MMMM = Full month name (August)
MMM  = Short month name (Aug)
MM   = 2-digit month (08)
dd   = 2-digit day (20)
HH   = 24-hour format (14)
hh   = 12-hour format (02)
mm   = Minutes (35)
ss   = Seconds (55)
```

### Common Data Types for CAST
```
INT          = Integer numbers
VARCHAR      = Text/String
DATE         = Date only
DATETIME     = Date with time
DECIMAL(x,y) = Decimal numbers
```

---

## Key Points to Remember

1. **Format codes are case-sensitive**: MM ≠ mm
2. **Formatting** changes appearance, **Casting** changes data type
3. SQL uses **ISO format** (yyyy-MM-dd) by default
4. Use `FORMAT()` for custom date displays
5. Use `CAST()` to change data types
6. Different countries have different date formats

---

## Practice Exercise

Try this yourself:

```sql
SELECT 
    GETDATE() AS Now,
    FORMAT(GETDATE(), 'dd/MM/yyyy') AS European,
    FORMAT(GETDATE(), 'MMMM dd, yyyy') AS Readable,
    CAST(GETDATE() AS DATE) AS Date_Only,
    FORMAT(12345.67, 'C') AS Money;
```

Now you understand formatting and casting! 🎉
