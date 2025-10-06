# SQL CASE Statement - Complete Beginner's Guide

## What is a CASE Statement?

A CASE statement is like an **"IF-THEN-ELSE"** decision maker in SQL. It helps you create conditional logic - checking conditions one by one and returning a value when a condition is met.

Think of it like a traffic light system:
- **IF** the light is green → **THEN** go
- **IF** the light is yellow → **THEN** slow down
- **IF** the light is red → **THEN** stop

---

## Basic Structure

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN condition3 THEN result3
    ELSE default_result
END
```

**Keywords explained:**
- **CASE** = Start of the logic (like "IF" in programming)
- **WHEN** = Check this condition
- **THEN** = If true, show this result
- **ELSE** = Default value if nothing matches (optional)
- **END** = End of the logic

---

## How SQL Processes CASE Statements

SQL checks conditions **from top to bottom** and **stops at the first TRUE condition**.

```
Check Condition 1 → TRUE? → Return Result 1 (STOP HERE!)
                 ↓ FALSE?
Check Condition 2 → TRUE? → Return Result 2 (STOP HERE!)
                 ↓ FALSE?
Check Condition 3 → TRUE? → Return Result 3 (STOP HERE!)
                 ↓ FALSE?
Return ELSE value (or NULL if no ELSE)
```

---

## Example 1: Simple One-Condition CASE

### Scenario: Classifying Sales Performance

```sql
SELECT 
    SalesAmount,
    CASE 
        WHEN SalesAmount > 50 THEN 'High'
    END AS Performance
FROM (
    VALUES 
        (60),
        (30),
        (15),
        (NULL)
) AS Sales(SalesAmount);
```

**Output:**
```
SalesAmount  Performance
-----------  -----------
60           High
30           NULL
15           NULL
NULL         NULL
```

**What happened?**
- 60 > 50? **YES** → Returns 'High'
- 30 > 50? **NO** → Returns NULL (no ELSE defined)
- 15 > 50? **NO** → Returns NULL
- NULL > 50? **NO** → Returns NULL

---

## Example 2: Two-Condition CASE

### Adding a Second Condition

```sql
SELECT 
    SalesAmount,
    CASE 
        WHEN SalesAmount > 50 THEN 'High'
        WHEN SalesAmount > 20 THEN 'Medium'
    END AS Performance
FROM (
    VALUES 
        (60),
        (30),
        (15),
        (NULL)
) AS Sales(SalesAmount);
```

**Output:**
```
SalesAmount  Performance
-----------  -----------
60           High
30           Medium
15           NULL
NULL         NULL
```

**What happened?**
- **60:** First check (60 > 50?) → **YES** → 'High' ✅ (stops here, doesn't check second condition)
- **30:** First check (30 > 50?) → **NO**, Second check (30 > 20?) → **YES** → 'Medium' ✅
- **15:** Both checks fail → NULL
- **NULL:** Both checks fail → NULL

---

## Example 3: Complete CASE with ELSE

### Adding a Default Value

```sql
SELECT 
    SalesAmount,
    CASE 
        WHEN SalesAmount > 50 THEN 'High'
        WHEN SalesAmount > 20 THEN 'Medium'
        ELSE 'Low'
    END AS Performance
FROM (
    VALUES 
        (60),
        (30),
        (15),
        (5)
) AS Sales(SalesAmount);
```

**Output:**
```
SalesAmount  Performance
-----------  -----------
60           High
30           Medium
15           Low
5            Low
```

**What happened?**
- **60:** > 50 → 'High' ✅
- **30:** Not > 50, but > 20 → 'Medium' ✅
- **15:** Fails both conditions → Uses ELSE → 'Low' ✅
- **5:** Fails both conditions → Uses ELSE → 'Low' ✅

---

## Real-World Example: Student Grade System

```sql
SELECT 
    StudentName,
    Marks,
    CASE 
        WHEN Marks >= 90 THEN 'A+ Excellent'
        WHEN Marks >= 80 THEN 'A Good'
        WHEN Marks >= 70 THEN 'B Average'
        WHEN Marks >= 60 THEN 'C Pass'
        ELSE 'F Fail'
    END AS Grade
FROM (
    VALUES 
        ('Ravi', 95),
        ('Priya', 82),
        ('Amit', 67),
        ('Sneha', 55)
) AS Students(StudentName, Marks);
```

**Output:**
```
StudentName  Marks  Grade
-----------  -----  ------------
Ravi         95     A+ Excellent
Priya        82     A Good
Amit         67     C Pass
Sneha        55     F Fail
```

---

## Real-World Example: Product Stock Status

```sql
SELECT 
    ProductName,
    StockQuantity,
    CASE 
        WHEN StockQuantity = 0 THEN 'Out of Stock'
        WHEN StockQuantity < 10 THEN 'Low Stock - Order Soon'
        WHEN StockQuantity < 50 THEN 'Medium Stock'
        ELSE 'Well Stocked'
    END AS StockStatus
FROM (
    VALUES 
        ('Laptop', 0),
        ('Mouse', 5),
        ('Keyboard', 25),
        ('Monitor', 100)
) AS Products(ProductName, StockQuantity);
```

**Output:**
```
ProductName  StockQuantity  StockStatus
-----------  -------------  ----------------------
Laptop       0              Out of Stock
Mouse        5              Low Stock - Order Soon
Keyboard     25             Medium Stock
Monitor      100            Well Stocked
```

---

## Real-World Example: Day of Week Classification

```sql
SELECT 
    EventDate,
    DATEPART(WEEKDAY, EventDate) AS DayNumber,
    CASE 
        WHEN DATEPART(WEEKDAY, EventDate) = 1 THEN 'Sunday - Weekend'
        WHEN DATEPART(WEEKDAY, EventDate) = 7 THEN 'Saturday - Weekend'
        ELSE 'Weekday'
    END AS DayType
FROM (
    VALUES 
        ('2025-10-11'),  -- Saturday
        ('2025-10-12'),  -- Sunday
        ('2025-10-13')   -- Monday
) AS Events(EventDate);
```

**Output:**
```
EventDate   DayNumber  DayType
----------  ---------  -----------------
2025-10-11  7          Saturday - Weekend
2025-10-12  1          Sunday - Weekend
2025-10-13  2          Weekday
```

---

## Important Rules to Remember

✅ **Order matters!** SQL checks from top to bottom  
✅ **Stops at first TRUE** - doesn't check remaining conditions  
✅ **ELSE is optional** - but without it, you get NULL for unmatched rows  
✅ **Always end with END** keyword  
✅ **Can use any condition** - >, <, =, !=, BETWEEN, IN, etc.  

---

## Common Mistakes to Avoid

### ❌ Wrong: Forgetting END
```sql
-- This will give an error!
CASE 
    WHEN Sales > 50 THEN 'High'
-- Missing END keyword!
```

### ✅ Correct:
```sql
CASE 
    WHEN Sales > 50 THEN 'High'
END
```

### ❌ Wrong: Wrong Order of Conditions
```sql
-- This won't work as expected!
CASE 
    WHEN Sales > 20 THEN 'Medium'  -- This catches everything > 20!
    WHEN Sales > 50 THEN 'High'    -- This never runs!
END
```

### ✅ Correct:
```sql
-- Put most specific condition first
CASE 
    WHEN Sales > 50 THEN 'High'    -- Check largest first
    WHEN Sales > 20 THEN 'Medium'  -- Then smaller
END
```

---

## Quick Comparison Table

| Feature | With ELSE | Without ELSE |
|---------|-----------|--------------|
| All conditions FALSE | Returns ELSE value | Returns NULL |
| Better practice? | ✅ Yes - clearer | ⚠️ Can work, but risky |

---

## Practice Challenge 🎯

Create a temperature classifier:

```sql
SELECT 
    Temperature,
    CASE 
        WHEN Temperature > 35 THEN 'Very Hot'
        WHEN Temperature > 25 THEN 'Hot'
        WHEN Temperature > 15 THEN 'Pleasant'
        WHEN Temperature > 5 THEN 'Cold'
        ELSE 'Very Cold'
    END AS Weather
FROM (
    VALUES (40), (28), (18), (8), (2)
) AS Temps(Temperature);
```

Try it yourself and see the results!

---

## Summary

CASE statements are perfect for:
- 📊 Categorizing data (High/Medium/Low)
- 🎓 Creating grade systems
- 📦 Checking stock levels
- 🚦 Building conditional logic
- 📈 Creating custom reports

**Remember:** CASE...WHEN...THEN...ELSE...END - It's like teaching SQL to make decisions! 🎉
