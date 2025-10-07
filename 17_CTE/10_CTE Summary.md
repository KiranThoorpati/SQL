# CTE Complete Summary - Everything We Learned!
*Your Complete Guide to Common Table Expressions*

## What is a CTE?

**CTE (Common Table Expression)** = A temporary, named result set that acts like a virtual table during your query

Think of it as a **sticky note with calculations** that you can use multiple times while solving a problem!

---

## Key Characteristics

✅ **Temporary** - Exists only during the query  
✅ **Named** - Has a clear, descriptive name  
✅ **Virtual table** - Acts like a table but isn't stored in database  
✅ **Reusable** - Can be used multiple times in one query  
✅ **Query-scoped** - Can't be used in other queries  

---

## Main Advantages of CTEs

### 1️⃣ **Improves Readability** 📖
Breaks complex queries into small, easy-to-understand pieces

### 2️⃣ **Introduces Modularity** 🧩
Each CTE is self-contained and solves one specific problem

### 3️⃣ **Reduces Redundancy** ♻️
Write logic once, use it multiple times

### 4️⃣ **Enables Looping** 🔄
Recursive CTEs allow iterations in SQL

### 5️⃣ **Acts Like a Table** 📊
Can be joined, filtered, and used just like physical tables

---

## All CTE Types We Learned

```
                    CTE Types
                        |
        ----------------+----------------
        |                               |
  Non-Recursive CTE              Recursive CTE
        |                               |
   -----+-----                    (Self-referencing,
   |         |                     loops until done)
Standalone  Nested
(Independent) (Depends on others)
```

---

## Complete Example: Student Performance Report

Let's create one comprehensive example showing ALL concepts!

### Our Tables:

**Students:**
```
student_id | name    | class
-----------|---------|------
1          | Alice   | 7A
2          | Bob     | 7A
3          | Charlie | 7B
4          | Diana   | 7B
```

**Scores:**
```
student_id | subject | score | exam_date
-----------|---------|-------|------------
1          | Math    | 85    | 2024-01-10
1          | Science | 92    | 2024-01-15
2          | Math    | 78    | 2024-01-10
2          | Science | 88    | 2024-01-15
3          | Math    | 95    | 2024-01-10
3          | Science | 90    | 2024-01-15
4          | Math    | 72    | 2024-01-10
4          | Science | 80    | 2024-01-15
```

---

## Example 1: Standalone CTE (Independent)

**Task:** Find students with average score above 85

```sql
-- Standalone CTE: Calculates averages independently
WITH StudentAverages AS (
    SELECT 
        student_id,
        AVG(score) as avg_score
    FROM scores
    GROUP BY student_id
)

-- Main query uses the CTE
SELECT 
    s.name,
    s.class,
    ROUND(sa.avg_score, 2) as average
FROM students s
JOIN StudentAverages sa ON s.student_id = sa.student_id
WHERE sa.avg_score > 85
ORDER BY sa.avg_score DESC;
```

**Expected Output:**
```
name    | class | average
--------|-------|--------
Charlie | 7B    | 92.50
Alice   | 7A    | 88.50
```

**Why it's standalone:** The CTE queries the database directly, doesn't depend on other CTEs

---

## Example 2: Multiple Standalone CTEs

**Task:** Show top performers and their latest exam date

```sql
-- CTE 1: Calculate averages (Standalone)
WITH StudentAverages AS (
    SELECT 
        student_id,
        AVG(score) as avg_score
    FROM scores
    GROUP BY student_id
),

-- CTE 2: Find latest exam date (Standalone)
LatestExam AS (
    SELECT 
        student_id,
        MAX(exam_date) as last_exam
    FROM scores
    GROUP BY student_id
)

-- Main query combines both CTEs
SELECT 
    s.name,
    s.class,
    ROUND(sa.avg_score, 2) as average,
    le.last_exam
FROM students s
JOIN StudentAverages sa ON s.student_id = sa.student_id
JOIN LatestExam le ON s.student_id = le.student_id
WHERE sa.avg_score >= 85
ORDER BY sa.avg_score DESC;
```

**Expected Output:**
```
name    | class | average | last_exam
--------|-------|---------|------------
Charlie | 7B    | 92.50   | 2024-01-15
Alice   | 7A    | 88.50   | 2024-01-15
```

**Both CTEs are standalone:** Each queries the database independently

---

## Example 3: Nested CTE (Dependent)

**Task:** Rank students and categorize by performance

```sql
-- CTE 1: Calculate averages (Standalone)
WITH StudentAverages AS (
    SELECT 
        student_id,
        AVG(score) as avg_score
    FROM scores
    GROUP BY student_id
),

-- CTE 2: Rank students (Nested - uses CTE 1)
RankedStudents AS (
    SELECT 
        student_id,
        avg_score,
        RANK() OVER (ORDER BY avg_score DESC) as rank
    FROM StudentAverages  -- ← Uses CTE 1!
),

-- CTE 3: Categorize performance (Nested - uses CTE 1)
PerformanceCategory AS (
    SELECT 
        student_id,
        avg_score,
        CASE 
            WHEN avg_score >= 90 THEN 'Excellent'
            WHEN avg_score >= 80 THEN 'Good'
            ELSE 'Fair'
        END as category
    FROM StudentAverages  -- ← Uses CTE 1!
)

-- Main query combines everything
SELECT 
    s.name,
    s.class,
    ROUND(rs.avg_score, 2) as average,
    rs.rank,
    pc.category
FROM students s
JOIN RankedStudents rs ON s.student_id = rs.student_id
JOIN PerformanceCategory pc ON s.student_id = pc.student_id
ORDER BY rs.rank;
```

**Expected Output:**
```
name    | class | average | rank | category
--------|-------|---------|------|----------
Charlie | 7B    | 92.50   | 1    | Excellent
Alice   | 7A    | 88.50   | 2    | Good
Bob     | 7A    | 83.00   | 3    | Good
Diana   | 7B    | 76.00   | 4    | Fair
```

**Why nested:** CTE 2 and CTE 3 both depend on CTE 1's results

---

## Example 4: Recursive CTE (Looping)

**Task:** Generate a grade scale from 60 to 100 (incrementing by 10)

```sql
WITH GradeScale AS (
    -- ANCHOR: Start at 60
    SELECT 60 as grade, 'D' as letter
    
    UNION ALL
    
    -- RECURSIVE: Add 10 each time
    SELECT 
        grade + 10,
        CASE 
            WHEN grade + 10 >= 90 THEN 'A'
            WHEN grade + 10 >= 80 THEN 'B'
            WHEN grade + 10 >= 70 THEN 'C'
            ELSE 'D'
        END
    FROM GradeScale
    WHERE grade < 100  -- Stop at 100
)

SELECT * FROM GradeScale
ORDER BY grade;
```

**Expected Output:**
```
grade | letter
------|-------
60    | D
70    | C
80    | B
90    | A
100   | A
```

**Why recursive:** The CTE calls itself, loops until grade reaches 100

---

## Example 5: Complete Student Hierarchy (Recursive)

**Mentorship Table:**
```
student_id | name    | mentor_id
-----------|---------|----------
1          | Alice   | NULL
2          | Bob     | 1
3          | Charlie | 1
4          | Diana   | 2
```

```sql
WITH StudentHierarchy AS (
    -- ANCHOR: Start with top mentor (no mentor_id)
    SELECT 
        student_id,
        name,
        mentor_id,
        1 as level,
        CAST(name AS VARCHAR(100)) as path
    FROM students
    WHERE mentor_id IS NULL
    
    UNION ALL
    
    -- RECURSIVE: Find mentees at each level
    SELECT 
        s.student_id,
        s.name,
        s.mentor_id,
        sh.level + 1,
        CAST(sh.path || ' → ' || s.name AS VARCHAR(100))
    FROM students s
    INNER JOIN StudentHierarchy sh 
        ON s.mentor_id = sh.student_id
)

SELECT 
    level,
    name,
    path as mentorship_chain
FROM StudentHierarchy
ORDER BY level, name;
```

**Expected Output:**
```
level | name    | mentorship_chain
------|---------|------------------
1     | Alice   | Alice
2     | Bob     | Alice → Bob
2     | Charlie | Alice → Charlie
3     | Diana   | Alice → Bob → Diana
```

---

## Best Practices Reminder

### ✅ DO:
- Use 3-5 CTEs per query (ideal range)
- Give CTEs clear, descriptive names
- Use CTEs to avoid repeating logic
- Break complex queries into manageable pieces

### ❌ DON'T:
- Create more than 5 CTEs (hard to read!)
- Use CTEs for simple queries
- Create a CTE for every tiny calculation
- Forget the stopping condition in recursive CTEs

---

## Quick Syntax Reference

### Standalone CTE:
```sql
WITH CTE_Name AS (
    SELECT ... FROM table
)
SELECT * FROM CTE_Name;
```

### Multiple CTEs:
```sql
WITH CTE_1 AS (...),
     CTE_2 AS (...),
     CTE_3 AS (...)
SELECT * FROM CTE_1;
```

### Nested CTE:
```sql
WITH CTE_1 AS (
    SELECT ... FROM table
),
CTE_2 AS (
    SELECT ... FROM CTE_1  -- Uses CTE_1
)
SELECT * FROM CTE_2;
```

### Recursive CTE:
```sql
WITH RECURSIVE CTE_Name AS (
    SELECT ... -- Anchor
    UNION ALL
    SELECT ... FROM CTE_Name WHERE condition
)
SELECT * FROM CTE_Name;
```

---

## Important Limitations

❌ **Cannot use ORDER BY** inside CTE (use in main query instead)  
❌ **Cannot query CTE from external queries** (exists only in current query)  
❌ **100 iteration default limit** for recursive CTEs (use MAXRECURSION to change)  

---

## Key Takeaways

📌 **CTE = Virtual table** during query execution  
📌 **Improves readability** by breaking complexity  
📌 **Enables modularity** - each CTE = one purpose  
📌 **Reduces redundancy** - write once, use multiple times  
📌 **Three types**: Standalone, Nested, Recursive  
📌 **Treat like tables** - join, filter, use normally  
📌 **Keep it simple** - 3-5 CTEs maximum per query  

---

## What's Next?

Now that you've mastered CTEs, you're ready to learn about **VIEWS** - which are like permanent CTEs stored in the database! 🎓

**Congratulations on completing CTEs!** 🎉 You now have a powerful tool to write clean, organized, and efficient SQL queries!
