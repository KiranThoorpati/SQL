# CTE Best Practices - Don't Overuse Them!
*Important Advice for Writing Better SQL*

## The Real Talk About CTEs ☕

CTEs are **amazing and powerful**, but there's an important lesson to learn:

> **"With great power comes great responsibility!"**

Many SQL developers (even experienced ones!) make a common mistake: **They use CTEs TOO MUCH!**

---

## The Overuse Problem

### What Happens When You Overuse CTEs?

Imagine your teacher asks you to write an essay. Instead of writing paragraphs, you write each sentence on a separate page with a title. Confusing, right?

**The Same Thing Happens with Too Many CTEs:**

❌ **Bad Example: Too Many CTEs**
```sql
-- Creating a CTE for EVERY small thing
WITH Step1 AS (
    SELECT * FROM students
),
Step2 AS (
    SELECT student_id FROM Step1
),
Step3 AS (
    SELECT student_id, 'Active' as status FROM Step2
),
Step4 AS (
    SELECT * FROM Step3 WHERE status = 'Active'
),
Step5 AS (
    SELECT student_id FROM Step4
)
-- ... and 15 more CTEs!

SELECT * FROM Step5;
```

**Problems:**
- 😵 **Hard to read** - Too many steps to follow
- 🐌 **Slower performance** - Uses lots of memory
- 🤯 **Confusing** - Even the person who wrote it gets lost!
- 📚 **Too complex** - Like reading a book with 100 tiny chapters

---

## The Golden Rules

### Rule #1: Don't Create a CTE for Every Little Thing

❌ **Bad Practice:**
```sql
-- Too many CTEs for simple tasks
WITH GetStudents AS (
    SELECT * FROM students
),
GetNames AS (
    SELECT student_id, name FROM GetStudents
),
AddUpperCase AS (
    SELECT student_id, UPPER(name) as name FROM GetNames
)
SELECT * FROM AddUpperCase;
```

✅ **Good Practice:**
```sql
-- One CTE does it all
WITH StudentData AS (
    SELECT 
        student_id, 
        UPPER(name) as name
    FROM students
)
SELECT * FROM StudentData;
```

---

### Rule #2: Merge CTEs When Possible

Think about **combining steps** instead of creating new CTEs for every calculation.

#### Example: Library Report

**Our Table:**
```
student_id | book_name    | pages | days_kept
-----------|--------------|-------|----------
1          | Math Book    | 200   | 10
1          | Science Book | 150   | 7
2          | History Book | 300   | 14
```

❌ **Bad: Too Many CTEs (4 CTEs!)**
```sql
-- CTE 1: Get books
WITH AllBooks AS (
    SELECT * FROM book_loans
),

-- CTE 2: Calculate total pages
TotalPages AS (
    SELECT 
        student_id,
        SUM(pages) as total_pages
    FROM AllBooks
    GROUP BY student_id
),

-- CTE 3: Calculate total days
TotalDays AS (
    SELECT 
        student_id,
        SUM(days_kept) as total_days
    FROM AllBooks
    GROUP BY student_id
),

-- CTE 4: Combine everything
Combined AS (
    SELECT 
        tp.student_id,
        tp.total_pages,
        td.total_days
    FROM TotalPages tp
    JOIN TotalDays td ON tp.student_id = td.student_id
)

SELECT * FROM Combined;
```

✅ **Good: One Efficient CTE**
```sql
-- Everything in ONE CTE
WITH StudentStats AS (
    SELECT 
        student_id,
        SUM(pages) as total_pages,
        SUM(days_kept) as total_days,
        ROUND(SUM(pages) * 1.0 / SUM(days_kept), 2) as pages_per_day
    FROM book_loans
    GROUP BY student_id
)

SELECT * FROM StudentStats;
```

**Expected Output:**
```
student_id | total_pages | total_days | pages_per_day
-----------|-------------|------------|---------------
1          | 350         | 17         | 20.59
2          | 300         | 14         | 21.43
```

**Why is this better?**
- ✅ Easier to read
- ✅ Faster execution
- ✅ Less memory used
- ✅ One clear purpose

---

### Rule #3: The Magic Number - Keep It Between 3-5 CTEs

**Recommended CTE Count:**

| Number of CTEs | Status | Action |
|----------------|--------|---------|
| 1-3 CTEs | ✅ Great! | Perfect amount |
| 3-5 CTEs | ⚠️ Okay | Still manageable |
| 5-10 CTEs | ❌ Too many! | Refactor and merge |
| 10+ CTEs | 🚨 Way too many! | Split into separate queries |

---

## Practical Example: When to Use CTEs

### Scenario: School Grade Report

**Tables:**
```
exam_scores:
student_id | subject | score
-----------|---------|------
1          | Math    | 85
1          | Science | 90
2          | Math    | 78
2          | Science | 88

students:
student_id | name    | class
-----------|---------|------
1          | Alice   | 7A
2          | Bob     | 7B
```

---

### ❌ Bad Example: Overusing CTEs (6 CTEs!)

```sql
WITH Step1 AS (
    SELECT * FROM exam_scores
),
Step2 AS (
    SELECT student_id, AVG(score) as avg_score FROM Step1 GROUP BY student_id
),
Step3 AS (
    SELECT * FROM students
),
Step4 AS (
    SELECT student_id, name FROM Step3
),
Step5 AS (
    SELECT * FROM Step2 JOIN Step4 USING(student_id)
),
Step6 AS (
    SELECT *, 
        CASE WHEN avg_score >= 85 THEN 'A' ELSE 'B' END as grade
    FROM Step5
)
SELECT * FROM Step6;
```

**Problems:**
- Too many steps for a simple task
- Hard to follow the logic
- Wastes memory on unnecessary intermediate results

---

### ✅ Good Example: Efficient Use (2 CTEs)

```sql
-- CTE 1: Calculate averages
WITH StudentAverages AS (
    SELECT 
        student_id,
        AVG(score) as avg_score
    FROM exam_scores
    GROUP BY student_id
),

-- CTE 2: Add grades and combine with student info
FinalReport AS (
    SELECT 
        s.name,
        s.class,
        ROUND(sa.avg_score, 2) as average,
        CASE 
            WHEN sa.avg_score >= 90 THEN 'A'
            WHEN sa.avg_score >= 80 THEN 'B'
            ELSE 'C'
        END as grade
    FROM students s
    JOIN StudentAverages sa ON s.student_id = sa.student_id
)

SELECT * FROM FinalReport
ORDER BY average DESC;
```

**Expected Output:**
```
name  | class | average | grade
------|-------|---------|------
Alice | 7A    | 87.50   | B
Bob   | 7B    | 83.00   | B
```

**Why is this better?**
- Clear, focused CTEs
- Each CTE has a specific purpose
- Easy to understand and modify
- Good performance

---

## When NOT to Use CTEs

### Simple Queries Don't Need CTEs!

❌ **Don't do this:**
```sql
WITH SimpleData AS (
    SELECT * FROM students WHERE class = '7A'
)
SELECT * FROM SimpleData;
```

✅ **Just write:**
```sql
SELECT * FROM students WHERE class = '7A';
```

**Rule of Thumb:** If your query is simple and doesn't need reuse or complex steps, **don't use a CTE!**

---

## Real-World Example: Sales Report

### Task: Create Monthly Sales Summary

**Sales Table:**
```
sale_id | product    | amount | sale_date
--------|------------|--------|------------
1       | Notebook   | 100    | 2024-01-15
2       | Pen        | 50     | 2024-01-20
3       | Notebook   | 120    | 2024-02-10
4       | Eraser     | 30     | 2024-02-15
```

### ✅ Good Approach (3 CTEs - Perfect!)

```sql
-- CTE 1: Extract month and calculate totals
WITH MonthlySales AS (
    SELECT 
        STRFTIME('%Y-%m', sale_date) as month,
        product,
        SUM(amount) as total_amount,
        COUNT(*) as sale_count
    FROM sales
    GROUP BY month, product
),

-- CTE 2: Calculate monthly totals
MonthlyTotals AS (
    SELECT 
        month,
        SUM(total_amount) as month_total
    FROM MonthlySales
    GROUP BY month
),

-- CTE 3: Add percentage contribution
FinalReport AS (
    SELECT 
        ms.month,
        ms.product,
        ms.total_amount,
        ms.sale_count,
        mt.month_total,
        ROUND(ms.total_amount * 100.0 / mt.month_total, 2) as percentage
    FROM MonthlySales ms
    JOIN MonthlyTotals mt ON ms.month = mt.month
)

SELECT * FROM FinalReport
ORDER BY month, total_amount DESC;
```

**Expected Output:**
```
month   | product  | total_amount | sale_count | month_total | percentage
--------|----------|--------------|------------|-------------|------------
2024-01 | Notebook | 100          | 1          | 150         | 66.67
2024-01 | Pen      | 50           | 1          | 150         | 33.33
2024-02 | Notebook | 120          | 1          | 150         | 80.00
2024-02 | Eraser   | 30           | 1          | 150         | 20.00
```

**Why is this good?**
- 3 CTEs - within the recommended range
- Each CTE has a clear, distinct purpose
- Readable and maintainable
- Good performance

---

## Quick Checklist Before Creating a CTE

Ask yourself:

1. ❓ **Is this really necessary?** Can I do it without a CTE?
2. ❓ **Can I merge this with another CTE?** Combine related calculations
3. ❓ **Is my query getting too complex?** More than 5 CTEs? Time to refactor!
4. ❓ **Does each CTE have a clear purpose?** If not, combine them
5. ❓ **Am I repeating myself?** Use CTEs to avoid repetition

---

## Summary: The CTE Wisdom

### ✅ DO:
- Use CTEs for complex, multi-step queries
- Keep between 3-5 CTEs per query
- Merge CTEs when possible
- Give CTEs clear, descriptive names
- Use CTEs to avoid repeating logic

### ❌ DON'T:
- Create a CTE for every tiny calculation
- Nest too many CTEs (avoid 10+ CTEs!)
- Use CTEs for simple SELECT statements
- Create CTEs just because you can
- Forget to refactor and consolidate

---

## Key Takeaways

📌 **CTEs are powerful** - but don't overuse them  
📌 **3-5 CTEs is ideal** - more than that, reconsider  
📌 **Merge when possible** - combine related logic  
📌 **Simple queries** - don't need CTEs  
📌 **Readability matters** - fewer CTEs = clearer code  
📌 **Think before adding** - is this CTE really necessary?  

**Remember:** Quality over quantity! A few well-designed CTEs are better than many confusing ones! 🎯
