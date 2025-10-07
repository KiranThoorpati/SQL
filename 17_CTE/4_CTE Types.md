# Types of CTEs (Common Table Expressions)
*A Beginner's Guide*

## The CTE Family Tree

Think of CTEs like a family with different members. Let me show you:

```
                    CTE Types
                       |
        ---------------+---------------
        |                             |
  Non-Recursive CTE            Recursive CTE
        |                          (Advanced - 
   -----+-----                   we'll skip for now)
   |         |
Standalone  Nested
  CTE       CTE
```

Today, we'll focus on the **Non-Recursive CTEs** - they're perfect for beginners!

---

## Type 1: Standalone CTE (The Simplest!)

A **Standalone CTE** is like having ONE helper doing ONE job. It's the easiest and most common type.

### Structure:
```
1. Define your CTE (helper)
2. Use it once in the main query
```

### Example 1: Finding Top Performers

**Our Class Grades Table:**
```
student_id | name      | subject    | marks
-----------|-----------|------------|-------
1          | Alice     | Math       | 95
2          | Bob       | Math       | 78
3          | Charlie   | Math       | 88
4          | Alice     | Science    | 92
5          | Bob       | Science    | 85
6          | Charlie   | Science    | 90
```

**SQL Query:**
```sql
-- Step 1: Create a standalone CTE to find top scorers
WITH TopScorers AS (
    SELECT name, subject, marks
    FROM grades
    WHERE marks >= 90
)

-- Step 2: Use it in main query
SELECT * FROM TopScorers
ORDER BY marks DESC;
```

**Expected Output:**
```
name    | subject  | marks
--------|----------|-------
Alice   | Math     | 95
Alice   | Science  | 92
Charlie | Science  | 90
```

**What happened?** 
- CTE found all students with 90+ marks
- Main query simply displayed them sorted by marks
- Simple and clean!

---

### Example 2: Calculating Subject Averages

**SQL Query:**
```sql
-- Standalone CTE to calculate averages
WITH SubjectAverages AS (
    SELECT 
        subject,
        AVG(marks) as average_marks,
        COUNT(*) as total_students
    FROM grades
    GROUP BY subject
)

-- Main query displays the results
SELECT 
    subject,
    ROUND(average_marks, 2) as avg_marks,
    total_students
FROM SubjectAverages;
```

**Expected Output:**
```
subject  | avg_marks | total_students
---------|-----------|---------------
Math     | 87.00     | 3
Science  | 89.00     | 3
```

---

## Type 2: Nested CTE (Multiple Helpers!)

A **Nested CTE** is like having MULTIPLE helpers, where each one does a different job. You can have 2, 3, or even more CTEs working together!

### Structure:
```
1. Define FIRST CTE (first helper)
2. Define SECOND CTE (second helper) - can use first CTE
3. Use them in the main query
```

### Example: Finding Students Better Than Average

**Our Table:**
```
student_id | name      | class | marks
-----------|-----------|-------|-------
1          | Alice     | 7A    | 85
2          | Bob       | 7A    | 72
3          | Charlie   | 7B    | 90
4          | Diana     | 7A    | 88
5          | Emma      | 7B    | 95
6          | Frank     | 7B    | 70
```

**SQL Query with Nested CTEs:**
```sql
-- First CTE: Calculate average marks
WITH ClassAverage AS (
    SELECT 
        class,
        AVG(marks) as avg_marks
    FROM students
    GROUP BY class
),

-- Second CTE: Find students above their class average
AboveAverage AS (
    SELECT 
        s.name,
        s.class,
        s.marks,
        ca.avg_marks
    FROM students s
    JOIN ClassAverage ca ON s.class = ca.class
    WHERE s.marks > ca.avg_marks
)

-- Main query: Display results
SELECT 
    name,
    class,
    marks,
    ROUND(avg_marks, 2) as class_average
FROM AboveAverage
ORDER BY class, marks DESC;
```

**Expected Output:**
```
name    | class | marks | class_average
--------|-------|-------|---------------
Diana   | 7A    | 88    | 81.67
Alice   | 7A    | 85    | 81.67
Emma    | 7B    | 95    | 85.00
Charlie | 7B    | 90    | 85.00
```

**What happened?**
1. **First CTE (ClassAverage):** Calculated average for each class
2. **Second CTE (AboveAverage):** Used the first CTE to find students scoring above their class average
3. **Main Query:** Displayed the final results

---

## Another Nested CTE Example: School Library System

**Books Table:**
```
book_id | title           | borrowed_count
--------|-----------------|---------------
1       | Harry Potter    | 25
2       | Math Fun        | 10
3       | Space Explorer  | 30
4       | Drawing Book    | 8
5       | Coding Kids     | 22
```

**SQL Query:**
```sql
-- First CTE: Find popular books (borrowed 15+ times)
WITH PopularBooks AS (
    SELECT book_id, title, borrowed_count
    FROM books
    WHERE borrowed_count >= 15
),

-- Second CTE: Categorize by popularity level
Categorized AS (
    SELECT 
        title,
        borrowed_count,
        CASE 
            WHEN borrowed_count >= 25 THEN 'Super Popular'
            ELSE 'Popular'
        END as popularity_level
    FROM PopularBooks
)

-- Main query: Show final categorized list
SELECT * FROM Categorized
ORDER BY borrowed_count DESC;
```

**Expected Output:**
```
title           | borrowed_count | popularity_level
----------------|----------------|------------------
Space Explorer  | 30             | Super Popular
Harry Potter    | 25             | Super Popular
Coding Kids     | 22             | Popular
```

---

## Quick Comparison Table

| Feature | Standalone CTE | Nested CTE |
|---------|----------------|------------|
| Number of CTEs | One | Two or more |
| Difficulty | Easy | Medium |
| Use When | Simple filtering/calculation | Complex multi-step logic |
| CTEs work together? | No | Yes |

---

## Real-World Analogies

**Standalone CTE:** 
- Like asking ONE friend to collect homework and give it to the teacher
- Simple, one-step process

**Nested CTE:**
- Like asking Friend #1 to collect homework, Friend #2 to organize it by subject, then giving it to the teacher
- Multiple steps, working together

---

## Key Takeaways

✅ **Standalone CTE** = Simplest form, one helper  
✅ **Nested CTE** = Multiple helpers working together  
✅ Both make queries **easier to read and understand**  
✅ You can have as many CTEs as you need in nested form  
✅ Each CTE can use the previous CTEs' results  

**Pro Tip:** Start with standalone CTEs, then move to nested ones as you get comfortable!
