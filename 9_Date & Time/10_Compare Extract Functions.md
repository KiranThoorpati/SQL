# Comparing SQL Date Extraction Functions - Quick Guide

## Understanding Output Data Types

When working with date functions, it's **super important** to know what type of output each function gives you!

### Quick Reference Table

| Function | Output Type | Example Output |
|----------|-------------|----------------|
| `DAY()`, `MONTH()`, `YEAR()` | **Integer** (Number) | 8, 20, 2025 |
| `DATEPART()` | **Integer** (Number) | 8, 20, 2025 |
| `DATENAME()` | **String** (Text) | "August", "Wednesday" |
| `DATETRUNC()` | **DateTime** (Date + Time) | 2025-08-01 00:00:00 |
| `EOMONTH()` | **Date** (Only Date) | 2025-08-31 |

---

## How to Choose the Right Function?

Here's a simple **decision flowchart** to help you choose:

```
START
  ↓
Do you need DAY, MONTH, or YEAR?
  ↓
  YES → Do you need it as a NUMBER?
         ↓
         YES → Use DAY(), MONTH(), or YEAR()
         ↓
         NO → Do you need the NAME?
               ↓
               YES → Use DATENAME()
  ↓
  NO → Do you need other parts (WEEK, QUARTER, etc.)?
        ↓
        YES → Use DATEPART()
        ↓
        NO → Do you need to reset date to a level?
              ↓
              YES → Use DATETRUNC()
              ↓
              NO → Do you need last day of month?
                    ↓
                    YES → Use EOMONTH()
```

---

## Complete Comparison Example

Let's use the date: **August 20, 2025, 14:35:55**

```sql
SELECT 
    '2025-08-20 14:35:55' AS OriginalDateTime,
    
    -- Extract YEAR
    YEAR('2025-08-20 14:35:55') AS Year_Function,
    DATEPART(YEAR, '2025-08-20 14:35:55') AS Year_DatePart,
    DATENAME(YEAR, '2025-08-20 14:35:55') AS Year_DateName,
    DATETRUNC(YEAR, '2025-08-20 14:35:55') AS Year_DateTrunc,
    
    -- Extract MONTH
    MONTH('2025-08-20 14:35:55') AS Month_Function,
    DATEPART(MONTH, '2025-08-20 14:35:55') AS Month_DatePart,
    DATENAME(MONTH, '2025-08-20 14:35:55') AS Month_DateName,
    DATETRUNC(MONTH, '2025-08-20 14:35:55') AS Month_DateTrunc,
    
    -- Extract DAY
    DAY('2025-08-20 14:35:55') AS Day_Function,
    DATEPART(DAY, '2025-08-20 14:35:55') AS Day_DatePart,
    DATENAME(DAY, '2025-08-20 14:35:55') AS Day_DateName,
    DATETRUNC(DAY, '2025-08-20 14:35:55') AS Day_DateTrunc;
```

**Output:**

```
OriginalDateTime    | 2025-08-20 14:35:55

Year_Function       | 2025          (Integer)
Year_DatePart       | 2025          (Integer)
Year_DateName       | 2025          (String)
Year_DateTrunc      | 2025-01-01 00:00:00  (DateTime)

Month_Function      | 8             (Integer)
Month_DatePart      | 8             (Integer)
Month_DateName      | August        (String)
Month_DateTrunc     | 2025-08-01 00:00:00  (DateTime)

Day_Function        | 20            (Integer)
Day_DatePart        | 20            (Integer)
Day_DateName        | 20            (String)
Day_DateTrunc       | 2025-08-20 00:00:00  (DateTime)
```

---

## Detailed Comparison Chart

### For MONTH Part

```sql
SELECT 
    GETDATE() AS CurrentDateTime,
    DATEPART(MONTH, GETDATE()) AS Month_DatePart,
    DATENAME(MONTH, GETDATE()) AS Month_DateName,
    DATETRUNC(MONTH, GETDATE()) AS Month_DateTrunc;
```

**Output Example (if today is August 20, 2025):**

```
CurrentDateTime     | Month_DatePart | Month_DateName | Month_DateTrunc
--------------------|----------------|----------------|--------------------
2025-08-20 14:35:55 | 8              | August         | 2025-08-01 00:00:00
```

**What Each Does:**
- `DATEPART(MONTH)` → Returns **8** (month number as integer)
- `DATENAME(MONTH)` → Returns **"August"** (month name as text)
- `DATETRUNC(MONTH)` → Returns **2025-08-01 00:00:00** (resets to first day of month)

---

## All Date Parts You Can Extract

Here's what parts you can use with DATEPART, DATENAME, and DATETRUNC:

```sql
SELECT 
    GETDATE() AS Now,
    
    -- Time Parts
    DATEPART(YEAR, GETDATE()) AS Year,
    DATEPART(QUARTER, GETDATE()) AS Quarter,
    DATEPART(MONTH, GETDATE()) AS Month,
    DATEPART(WEEK, GETDATE()) AS Week,
    DATEPART(DAY, GETDATE()) AS Day,
    DATEPART(DAYOFYEAR, GETDATE()) AS DayOfYear,
    DATEPART(WEEKDAY, GETDATE()) AS Weekday,
    DATEPART(HOUR, GETDATE()) AS Hour,
    DATEPART(MINUTE, GETDATE()) AS Minute,
    DATEPART(SECOND, GETDATE()) AS Second;
```

**Sample Output:**

```
Now                 | 2025-08-20 14:35:55
Year                | 2025
Quarter             | 3
Month               | 8
Week                | 34
Day                 | 20
DayOfYear           | 232
Weekday             | 4 (Wednesday)
Hour                | 14
Minute              | 35
Second              | 55
```

---

## Special Parts (Only with DATEPART/DATENAME)

Some parts **only work** with DATEPART and DATENAME:

### Example: QUARTER

```sql
SELECT 
    '2025-01-15' AS Date1,
    DATEPART(QUARTER, '2025-01-15') AS Quarter1,
    
    '2025-08-20' AS Date2,
    DATEPART(QUARTER, '2025-08-20') AS Quarter2;
```

**Output:**

```
Date1      | Quarter1 | Date2      | Quarter2
-----------|----------|------------|----------
2025-01-15 | 1        | 2025-08-20 | 3
```

**Quarters Explained:**
- Q1 = January, February, March (Months 1-3)
- Q2 = April, May, June (Months 4-6)
- Q3 = July, August, September (Months 7-9)
- Q4 = October, November, December (Months 10-12)

---

### Example: WEEKDAY Names

```sql
SELECT 
    '2025-08-18' AS Date1,
    DATENAME(WEEKDAY, '2025-08-18') AS Day1,
    
    '2025-08-19' AS Date2,
    DATENAME(WEEKDAY, '2025-08-19') AS Day2,
    
    '2025-08-20' AS Date3,
    DATENAME(WEEKDAY, '2025-08-20') AS Day3;
```

**Output:**

```
Date1      | Day1     | Date2      | Day2    | Date3      | Day3
-----------|----------|------------|---------|------------|----------
2025-08-18 | Monday   | 2025-08-19 | Tuesday | 2025-08-20 | Wednesday
```

---

## Practical Comparison Example

Let's solve a real problem using different functions:

**Task**: Show order information with different date formats

```sql
SELECT 
    OrderID,
    OrderDate,
    
    -- Using simple functions
    YEAR(OrderDate) AS Order_Year,
    MONTH(OrderDate) AS Order_Month_Number,
    DAY(OrderDate) AS Order_Day,
    
    -- Using DATENAME for readable format
    DATENAME(MONTH, OrderDate) AS Order_Month_Name,
    DATENAME(WEEKDAY, OrderDate) AS Order_DayName,
    
    -- Using DATEPART for special parts
    DATEPART(QUARTER, OrderDate) AS Order_Quarter,
    DATEPART(WEEK, OrderDate) AS Order_Week,
    
    -- Using DATETRUNC for aggregation
    DATETRUNC(MONTH, OrderDate) AS Month_Start,
    
    -- Using EOMONTH for month end
    EOMONTH(OrderDate) AS Month_End
    
FROM SalesOrders
WHERE OrderID <= 3;
```

**Output:**

```
OrderID | OrderDate  | Order_Year | Order_Month_Number | Order_Day | Order_Month_Name | Order_DayName | Order_Quarter | Order_Week | Month_Start         | Month_End
--------|------------|------------|--------------------|-----------|--------------------|---------------|---------------|------------|---------------------|------------
1       | 2025-01-05 | 2025       | 1                  | 5         | January           | Sunday        | 1             | 2          | 2025-01-01 00:00:00 | 2025-01-31
2       | 2025-01-15 | 2025       | 1                  | 15        | January           | Wednesday     | 1             | 3          | 2025-01-01 00:00:00 | 2025-01-31
3       | 2025-01-22 | 2025       | 1                  | 22        | January           | Wednesday     | 1             | 4          | 2025-01-01 00:00:00 | 2025-01-31
```

---

## When to Use Each Function - Simple Rules

### Use `YEAR()`, `MONTH()`, `DAY()` when:
✅ You need a **simple number**  
✅ You're working with year, month, or day only  
✅ You want **fast and easy** code

### Use `DATEPART()` when:
✅ You need **special parts** like QUARTER, WEEK  
✅ You want one function for everything  
✅ You need **integer output**

### Use `DATENAME()` when:
✅ You need **readable names** (August, Wednesday)  
✅ You're creating **reports for people to read**  
✅ You want **text output**

### Use `DATETRUNC()` when:
✅ You need to **group data** by time periods  
✅ You're doing **aggregations and summaries**  
✅ You want the **full datetime** at a certain level

### Use `EOMONTH()` when:
✅ You need the **last day of a month**  
✅ You're calculating **month-end reports**

---

## Memory Trick! 🧠

**Think of it like this:**

- **YEAR/MONTH/DAY** = Quick shortcuts (fast food! 🍔)
- **DATEPART** = Swiss Army knife (one tool, many uses! 🔧)
- **DATENAME** = Translator (numbers → words! 📖)
- **DATETRUNC** = Time machine reset button (rewind to a point! ⏰)
- **EOMONTH** = Calendar page flip (jump to last day! 📅)

---

## Key Points to Remember

1. **Know your output types**: Integer, String, DateTime, or Date
2. **Use simple functions** when possible: YEAR(), MONTH(), DAY()
3. **Use DATEPART** for special parts: QUARTER, WEEK, DAYOFYEAR
4. **Use DATENAME** for reports: Makes data easy to read
5. **Use DATETRUNC** for grouping: Perfect for aggregations
6. **Different tools for different jobs**: Choose based on what you need!

---

## Practice Exercise

Try this comprehensive query:

```sql
DECLARE @TestDate DATETIME = '2025-08-20 14:35:55';

SELECT 
    @TestDate AS OriginalDate,
    YEAR(@TestDate) AS Simple_Year,
    DATENAME(MONTH, @TestDate) AS Readable_Month,
    DATEPART(QUARTER, @TestDate) AS Which_Quarter,
    DATETRUNC(DAY, @TestDate) AS Reset_To_Day,
    EOMONTH(@TestDate) AS Last_Day_Of_Month;
```

Now you understand all the date extraction functions and when to use each one! 🎉
