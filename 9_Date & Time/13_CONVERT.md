# Understanding SQL CONVERT Function - Complete Guide

## What is CONVERT?

**CONVERT** is a powerful SQL function that does **two jobs**:
1. **Changes data types** (like CAST)
2. **Formats values** (like FORMAT)

It's like a 2-in-1 tool - you can use it for just one job or both!

## Basic Syntax

```sql
CONVERT(data_type, value, [style])
```

**Parameters:**
1. **data_type**: What type you want to convert to (INT, VARCHAR, DATE, etc.)
2. **value**: The value you want to convert
3. **style** (optional): Format/style number for formatting dates

---

## Example 1: String to Integer (Casting Only)

```sql
SELECT 
    '123' AS OriginalString,
    CONVERT(INT, '123') AS [String to Integer Convert];
```

**Output:**
```
OriginalString | String to Integer Convert
---------------|-------------------------
123            | 123
```

The first is text, the second is a number you can use in math!

---

## Example 2: String to Date (Casting Only)

```sql
SELECT 
    '2025-08-20' AS OriginalString,
    CONVERT(DATE, '2025-08-20') AS [String to Date Convert];
```

**Output:**
```
OriginalString | String to Date Convert
---------------|----------------------
2025-08-20     | 2025-08-20
```

Now it's a real date, not just text!

---

## Example 3: DateTime to Date (Remove Time)

```sql
SELECT 
    OrderID,
    CreationTime,
    CONVERT(DATE, CreationTime) AS [DateTime to Date Convert]
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05 10:30:45' AS DATETIME) AS CreationTime
    UNION ALL
    SELECT 2, CAST('2025-02-15 14:20:30' AS DATETIME)
    UNION ALL
    SELECT 3, CAST('2025-03-22 09:15:55' AS DATETIME)
) AS Orders;
```

**Output:**
```
OrderID | CreationTime        | DateTime to Date Convert
--------|---------------------|-------------------------
1       | 2025-01-05 10:30:45 | 2025-01-05
2       | 2025-02-15 14:20:30 | 2025-02-15
3       | 2025-03-22 09:15:55 | 2025-03-22
```

Time information is removed!

---

## Example 4: Date to String with USA Format (Casting + Formatting)

Now let's use CONVERT for **both** casting AND formatting:

```sql
SELECT 
    OrderID,
    CreationTime,
    CONVERT(VARCHAR, CreationTime, 101) AS [USA Format Style 101]
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05 10:30:45' AS DATETIME) AS CreationTime
    UNION ALL
    SELECT 2, CAST('2025-02-15 14:20:30' AS DATETIME)
    UNION ALL
    SELECT 3, CAST('2025-03-22 09:15:55' AS DATETIME)
) AS Orders;
```

**Output:**
```
OrderID | CreationTime        | USA Format Style 101
--------|---------------------|---------------------
1       | 2025-01-05 10:30:45 | 01/05/2025
2       | 2025-02-15 14:20:30 | 02/15/2025
3       | 2025-03-22 09:15:55 | 03/22/2025
```

Converted to text AND formatted in USA style (MM/DD/YYYY)!

---

## Example 5: Date to String with European Format

```sql
SELECT 
    OrderID,
    CreationTime,
    CONVERT(VARCHAR, CreationTime, 103) AS [European Format Style 103]
FROM (
    SELECT 1 AS OrderID, CAST('2025-01-05 10:30:45' AS DATETIME) AS CreationTime
    UNION ALL
    SELECT 2, CAST('2025-02-15 14:20:30' AS DATETIME)
    UNION ALL
    SELECT 3, CAST('2025-03-22 09:15:55' AS DATETIME)
) AS Orders;
```

**Output:**
```
OrderID | CreationTime        | European Format Style 103
--------|---------------------|-------------------------
1       | 2025-01-05 10:30:45 | 05/01/2025
2       | 2025-02-15 14:20:30 | 15/02/2025
3       | 2025-03-22 09:15:55 | 22/03/2025
```

European style (DD/MM/YYYY)!

---

## Popular Style Numbers for Dates

### Without Century (2-digit year)

| Style | Format | Example |
|-------|--------|---------|
| 1 | MM/DD/YY | 01/05/25 |
| 3 | DD/MM/YY | 05/01/25 |
| 4 | DD.MM.YY | 05.01.25 |
| 5 | DD-MM-YY | 05-01-25 |
| 6 | DD Mon YY | 05 Jan 25 |
| 7 | Mon DD, YY | Jan 05, 25 |

### With Century (4-digit year)

| Style | Format | Example |
|-------|--------|---------|
| 101 | MM/DD/YYYY | 01/05/2025 |
| 103 | DD/MM/YYYY | 05/01/2025 |
| 104 | DD.MM.YYYY | 05.01.2025 |
| 105 | DD-MM-YYYY | 05-01-2025 |
| 106 | DD Mon YYYY | 05 Jan 2025 |
| 107 | Mon DD, YYYY | Jan 05, 2025 |
| 110 | MM-DD-YYYY | 01-05-2025 |
| 111 | YYYY/MM/DD | 2025/01/05 |
| 112 | YYYYMMDD | 20250105 |

---

## Complete Comparison: Different Styles

```sql
SELECT 
    CAST('2025-08-20 14:35:55' AS DATETIME) AS OriginalDateTime,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 1) AS Style_1,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 3) AS Style_3,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 6) AS Style_6,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 101) AS Style_101,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 103) AS Style_103,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 112) AS Style_112;
```

**Output:**
```
OriginalDateTime    | Style_1    | Style_3    | Style_6      | Style_101  | Style_103  | Style_112
--------------------|------------|------------|--------------|------------|------------|----------
2025-08-20 14:35:55 | 08/20/25   | 20/08/25   | 20 Aug 25    | 08/20/2025 | 20/08/2025 | 20250820
```

---

## Time Formats

You can also format time information:

```sql
SELECT 
    CAST('2025-08-20 14:35:55' AS DATETIME) AS OriginalDateTime,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 8) AS Style_8_Time,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 108) AS Style_108_Time,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 114) AS Style_114_Time;
```

**Output:**
```
OriginalDateTime    | Style_8_Time | Style_108_Time | Style_114_Time
--------------------|--------------|----------------|------------------
2025-08-20 14:35:55 | 14:35:55     | 14:35:55       | 14:35:55.000
```

---

## Date and Time Combined

```sql
SELECT 
    CAST('2025-08-20 14:35:55' AS DATETIME) AS OriginalDateTime,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 0) AS Style_0,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 100) AS Style_100,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 120) AS Style_120,
    CONVERT(VARCHAR, CAST('2025-08-20 14:35:55' AS DATETIME), 121) AS Style_121;
```

**Output:**
```
OriginalDateTime    | Style_0             | Style_100              | Style_120           | Style_121
--------------------|---------------------|------------------------|---------------------|---------------------
2025-08-20 14:35:55 | Aug 20 2025  2:35PM | Aug 20 2025  2:35PM    | 2025-08-20 14:35:55 | 2025-08-20 14:35:55.000
```

---

## Real-World Example: Order Report

```sql
SELECT 
    OrderID,
    OrderDate,
    OrderAmount,
    
    -- Original format
    OrderDate AS Original_Format,
    
    -- USA format
    CONVERT(VARCHAR, OrderDate, 101) AS USA_Format,
    
    -- European format
    CONVERT(VARCHAR, OrderDate, 103) AS European_Format,
    
    -- Readable format
    CONVERT(VARCHAR, OrderDate, 106) AS Readable_Format,
    
    -- ISO format (no separators)
    CONVERT(VARCHAR, OrderDate, 112) AS ISO_Format
    
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
OrderID | OrderDate  | OrderAmount | Original_Format | USA_Format | European_Format | Readable_Format | ISO_Format
--------|------------|-------------|-----------------|------------|-----------------|-----------------|------------
1       | 2025-01-15 | 1234.50     | 2025-01-15      | 01/15/2025 | 15/01/2025      | 15 Jan 2025     | 20250115
2       | 2025-02-20 | 5678.99     | 2025-02-20      | 02/20/2025 | 20/02/2025      | 20 Feb 2025     | 20250220
3       | 2025-03-10 | 999.00      | 2025-03-10      | 03/10/2025 | 10/03/2025      | 10 Mar 2025     | 20250310
```

---

## CONVERT vs CAST vs FORMAT

### When to Use Each?

#### Use CAST when:
- You only need to change data type
- You want simple, readable code
- You don't need formatting

```sql
CAST('123' AS INT)
CAST('2025-08-20' AS DATE)
```

#### Use CONVERT when:
- You need to change data type AND format
- You want to use style numbers
- You're working with dates in SQL Server

```sql
CONVERT(VARCHAR, OrderDate, 101)  -- Casting + Formatting
CONVERT(INT, '123')               -- Just Casting
```

#### Use FORMAT when:
- You need custom formats
- You want more flexibility
- You're comfortable with format codes

```sql
FORMAT(OrderDate, 'MM/dd/yyyy')
FORMAT(OrderDate, 'MMMM dd, yyyy')
```

---

## Complete Comparison Example

```sql
SELECT 
    CAST('2025-08-20' AS DATE) AS OriginalDate,
    
    -- Using CAST (only changes type)
    CAST(CAST('2025-08-20' AS DATE) AS VARCHAR) AS Using_CAST,
    
    -- Using CONVERT (changes type + format)
    CONVERT(VARCHAR, CAST('2025-08-20' AS DATE), 101) AS Using_CONVERT,
    
    -- Using FORMAT (custom format)
    FORMAT(CAST('2025-08-20' AS DATE), 'MM/dd/yyyy') AS Using_FORMAT;
```

**Output:**
```
OriginalDate | Using_CAST | Using_CONVERT | Using_FORMAT
-------------|------------|---------------|-------------
2025-08-20   | 2025-08-20 | 08/20/2025    | 08/20/2025
```

---

## Common Data Type Conversions

### String to Number
```sql
SELECT CONVERT(INT, '123') AS Result;        -- Output: 123
SELECT CONVERT(DECIMAL(10,2), '123.45');     -- Output: 123.45
```

### Number to String
```sql
SELECT CONVERT(VARCHAR, 123) AS Result;      -- Output: '123'
SELECT CONVERT(VARCHAR, 123.45);             -- Output: '123.45'
```

### String to Date
```sql
SELECT CONVERT(DATE, '2025-08-20');          -- Output: 2025-08-20
SELECT CONVERT(DATETIME, '2025-08-20 14:35:55');
```

### Date to String
```sql
SELECT CONVERT(VARCHAR, GETDATE(), 101);     -- Output: MM/DD/YYYY
SELECT CONVERT(VARCHAR, GETDATE(), 103);     -- Output: DD/MM/YYYY
```

---

## Key Points to Remember

1. **CONVERT does two jobs**: Casting AND Formatting
2. **Style parameter is optional**: If not provided, uses default (0)
3. **Style numbers are predefined**: Can't create custom ones like FORMAT
4. **Faster than FORMAT**: Because it uses numbers instead of parsing text
5. **SQL Server specific**: Other databases might not have it
6. **Popular for dates**: Lots of built-in date format styles

---

## Quick Reference: Most Used Styles

```
Date Formats (with 4-digit year):
101 = MM/DD/YYYY    (USA)
103 = DD/MM/YYYY    (Europe)
105 = DD-MM-YYYY    (Dashes)
106 = DD Mon YYYY   (Readable)
112 = YYYYMMDD      (No separators)
120 = YYYY-MM-DD    (ISO Standard)

Time Formats:
108 = HH:MM:SS      (24-hour)
114 = HH:MM:SS.mmm  (With milliseconds)
```

---

## Practice Exercise

Try this complete example:

```sql
SELECT 
    '2025-08-20 14:35:55' AS Original,
    CONVERT(DATE, '2025-08-20 14:35:55') AS Just_Date,
    CONVERT(VARCHAR, '2025-08-20 14:35:55', 101) AS USA_Style,
    CONVERT(VARCHAR, '2025-08-20 14:35:55', 103) AS Europe_Style,
    CONVERT(VARCHAR, '2025-08-20 14:35:55', 106) AS Readable,
    CONVERT(INT, '123') AS Text_To_Number;
```

Now you understand the CONVERT function completely!
