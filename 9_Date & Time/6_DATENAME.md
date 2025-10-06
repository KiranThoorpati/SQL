# Understanding SQL DATENAME Function

## What is DATENAME?

The **DATENAME** function in SQL helps us get the **name** of different parts of a date instead of just numbers. It makes dates easier to read and understand!

## The Difference Between DATEPART and DATENAME

Before we learn DATENAME, let's understand the difference:

- **DATEPART**: Returns **numbers** (integers)
  - Example: Month → 8, Day → 20
  
- **DATENAME**: Returns **names** (text/strings)
  - Example: Month → "August", Day → "Wednesday"

## Basic Syntax

```sql
DATENAME(part, date)
```

- **part**: Which part of the date you want (month, weekday, year, etc.)
- **date**: The date you're working with

## Examples with Expected Outputs

### Example 1: Getting Month Name

```sql
SELECT DATENAME(MONTH, '2024-08-20') AS MonthName;
```

**Output:**
```
MonthName
---------
August
```

Instead of getting the number "8", you get the full name "August"!

---

### Example 2: Getting Day Name

```sql
SELECT DATENAME(WEEKDAY, '2024-08-20') AS DayName;
```

**Output:**
```
DayName
---------
Wednesday
```

You get "Wednesday" instead of the number "20"!

---

### Example 3: Working with a Table

Let's say we have a table called **Orders**:

```sql
-- Sample data
OrderID  |  CreationTime
---------|------------------
1        |  2024-01-15
2        |  2024-02-05
3        |  2024-03-10
4        |  2024-04-20
```

Now, let's get the month names:

```sql
SELECT 
    OrderID,
    CreationTime,
    DATENAME(MONTH, CreationTime) AS MonthName
FROM Orders;
```

**Output:**
```
OrderID | CreationTime | MonthName
--------|--------------|----------
1       | 2024-01-15   | January
2       | 2024-02-05   | February
3       | 2024-03-10   | March
4       | 2024-04-20   | April
```

---

### Example 4: Getting Weekday Names

```sql
SELECT 
    OrderID,
    CreationTime,
    DATENAME(WEEKDAY, CreationTime) AS WeekdayName
FROM Orders;
```

**Output:**
```
OrderID | CreationTime | WeekdayName
--------|--------------|------------
1       | 2024-01-15   | Monday
2       | 2024-02-05   | Monday
3       | 2024-03-10   | Sunday
4       | 2024-04-20   | Saturday
```

---

### Example 5: What About Day of Month?

```sql
SELECT DATENAME(DAY, '2024-08-20') AS DayNumber;
```

**Output:**
```
DayNumber
---------
20
```

**Important Note**: Days of the month don't have names, so you still get numbers! But these numbers are stored as **text (string)**, not as **integers**.

---

### Example 6: Getting Year

```sql
SELECT DATENAME(YEAR, '2024-08-20') AS YearValue;
```

**Output:**
```
YearValue
---------
2024
```

Again, years don't have names, so you get numbers stored as text!

---

## Complete Example with Multiple Parts

```sql
SELECT 
    OrderID,
    CreationTime,
    DATENAME(YEAR, CreationTime) AS Year,
    DATENAME(MONTH, CreationTime) AS MonthName,
    DATENAME(DAY, CreationTime) AS DayNumber,
    DATENAME(WEEKDAY, CreationTime) AS WeekdayName
FROM Orders;
```

**Output:**
```
OrderID | CreationTime | Year | MonthName | DayNumber | WeekdayName
--------|--------------|------|-----------|-----------|------------
1       | 2024-01-15   | 2024 | January   | 15        | Monday
2       | 2024-02-05   | 2024 | February  | 5         | Monday
3       | 2024-03-10   | 2024 | March     | 10        | Sunday
4       | 2024-04-20   | 2024 | April     | 20        | Saturday
```

---

## Why Use DATENAME?

### ❌ Not Easy to Read:
```
Sales Report by Month
Month: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12
```

### ✅ Easy to Read:
```
Sales Report by Month
Month: January, February, March, April, May, June...
```

**DATENAME makes reports more user-friendly and easier to understand!**

---

## Key Points to Remember

1. **DATENAME returns text (strings)**, not numbers
2. **MONTH** → Full month name (January, February, etc.)
3. **WEEKDAY** → Full day name (Monday, Tuesday, etc.)
4. **DAY** and **YEAR** → Numbers as text (since they don't have names)
5. Perfect for creating **readable reports** for users!

---

## Practice Exercise

Try this query yourself:

```sql
SELECT 
    DATENAME(MONTH, GETDATE()) AS CurrentMonth,
    DATENAME(WEEKDAY, GETDATE()) AS Today;
```

This will show you the current month name and today's day name!
