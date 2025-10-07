# Views vs CTEs: When to Use What?
*Understanding the Key Differences*

## Quick Visual Comparison

```
CTE 📝                          VIEW 🗄️
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⏱️ Temporary                    💾 Persistent
📄 One query only               🌐 Multiple queries
🗑️ Auto-deleted                ♻️ Stays in database
🎯 Local importance             ⭐ Project-wide importance
🚀 Quick & easy                 🔧 Needs maintenance
💭 Persists nothing             📚 Persists LOGIC
```

---

## The Core Difference

### CTEs: Reduce Redundancy WITHIN One Query
```
ONE QUERY {
    CTE 1 → Used multiple times
    CTE 2 → Used multiple times
    Main Query → Uses CTEs
}
Scope: Single query only ✅
```

### Views: Reduce Redundancy ACROSS Multiple Queries
```
VIEW (in database)
    ↓
Query 1 uses it
Query 2 uses it
Query 3 uses it
Query 100+ uses it
Scope: Entire project ✅
```

---

## Key Concept: Persisting Logic vs Data

### Tables Persist DATA:
```sql
CREATE TABLE Students (...);
INSERT INTO Students VALUES (...);
-- Data stored permanently 💾
```

### Views Persist LOGIC:
```sql
CREATE VIEW StudentReport AS
SELECT ... FROM ... WHERE ...;
-- Logic stored permanently 📚
```

### CTEs Persist NOTHING:
```sql
WITH TempCalc AS (
    SELECT ... FROM ...
)
SELECT * FROM TempCalc;
-- Disappears after query! 💨
```

---

## Complete Working Example

### Setup: Create Tables

```sql
-- Create database
CREATE DATABASE SchoolSystem;
USE SchoolSystem;

-- Tables (Persist DATA)
CREATE TABLE Students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    class VARCHAR(10)
);

CREATE TABLE Grades (
    grade_id INT PRIMARY KEY,
    student_id INT,
    subject VARCHAR(30),
    marks INT
);

-- Insert data
INSERT INTO Students VALUES
(1, 'Alice', '7A'),
(2, 'Bob', '7B'),
(3, 'Charlie', '7A'),
(4, 'Diana', '8A');

INSERT INTO Grades VALUES
(1, 1, 'Math', 85),
(2, 1, 'Science', 92),
(3, 2, 'Math', 78),
(4, 2, 'Science', 88),
(5, 3, 'Math', 95),
(6, 3, 'Science', 90),
(7, 4, 'Math', 88),
(8, 4, 'Science', 94);
```

---

## Scenario 1: Logic Used in ONE Query Only → Use CTE

### Task: One-time analysis of top performers

```sql
-- CTE: Perfect for one-time use
WITH StudentAverages AS (
    SELECT 
        s.student_id,
        s.name,
        AVG(g.marks) as avg_marks
    FROM Students s
    JOIN Grades g ON s.student_id = g.student_id
    GROUP BY s.student_id, s.name
),
TopPerformers AS (
    SELECT 
        name,
        avg_marks,
        RANK() OVER (ORDER BY avg_marks DESC) as rank
    FROM StudentAverages
    WHERE avg_marks >= 85
)
SELECT * FROM TopPerformers;
```

**Output:**
```
name    | avg_marks | rank
--------|-----------|-----
Diana   | 91.00     | 1
Charlie | 92.50     | 2
Alice   | 88.50     | 3
```

**After query completes:**
- ✅ Results shown
- 🗑️ CTEs automatically deleted
- 💨 No trace left in database

**When to use CTE:**
- ✅ Logic needed only once
- ✅ Temporary calculation
- ✅ Not important for other queries
- ✅ Quick analysis

---

## Scenario 2: Logic Used in MANY Queries → Use VIEW

### Task: Many people need student averages regularly

#### WITHOUT View (Repetitive):
```sql
-- Teacher 1 needs it
WITH StudentAvg AS (
    SELECT s.name, AVG(g.marks) as avg
    FROM Students s JOIN Grades g ON s.student_id = g.student_id
    GROUP BY s.name
)
SELECT * FROM StudentAvg WHERE avg > 90;

-- Teacher 2 needs it (DUPLICATE!)
WITH StudentAvg AS (
    SELECT s.name, AVG(g.marks) as avg
    FROM Students s JOIN Grades g ON s.student_id = g.student_id
    GROUP BY s.name
)
SELECT * FROM StudentAvg WHERE avg < 80;

-- Principal needs it (DUPLICATE AGAIN!)
WITH StudentAvg AS (
    SELECT s.name, AVG(g.marks) as avg
    FROM Students s JOIN Grades g ON s.student_id = g.student_id
    GROUP BY s.name
)
SELECT AVG(avg) FROM StudentAvg;
```

**Problem:** Same logic written 3 times! ❌

---

#### WITH View (Smart):
```sql
-- Create VIEW once (Persist the LOGIC)
CREATE VIEW StudentAverages AS
SELECT 
    s.student_id,
    s.name,
    s.class,
    AVG(g.marks) as average_marks,
    COUNT(g.grade_id) as subjects_count
FROM Students s
JOIN Grades g ON s.student_id = g.student_id
GROUP BY s.student_id, s.name, s.class;

-- Teacher 1 (Simple!)
SELECT * FROM StudentAverages WHERE average_marks > 90;

-- Teacher 2 (Simple!)
SELECT * FROM StudentAverages WHERE average_marks < 80;

-- Principal (Simple!)
SELECT AVG(average_marks) FROM StudentAverages;
```

**Benefits:**
- ✅ Logic written once
- ✅ Used everywhere
- ✅ Stays in database
- ✅ Easy maintenance

**When to use VIEW:**
- ✅ Logic used by many queries
- ✅ Important for entire project
- ✅ Needs to be reusable
- ✅ Long-term use

---

## Maintenance Comparison

### CTE Maintenance (EASY):
```sql
WITH MyCalc AS (
    SELECT ... FROM ...
)
SELECT * FROM MyCalc;
-- Query ends → CTE auto-deleted ✅
-- No cleanup needed! 🎉
```

**Steps:**
1. Write CTE
2. Use it
3. Done! (Auto cleanup)

---

### VIEW Maintenance (MORE WORK):
```sql
-- Step 1: Create
CREATE VIEW MyReport AS SELECT ... FROM ...;

-- Step 2: Use it
SELECT * FROM MyReport;

-- Step 3: Update when needed
ALTER VIEW MyReport AS SELECT ... FROM ...;

-- Step 4: Drop when done
DROP VIEW MyReport;
```

**Steps:**
1. CREATE view
2. Use it
3. ALTER when logic changes
4. DROP when no longer needed

**More steps = More maintenance ⚠️**

---

## Decision Tree: CTE or View?

```
Is this logic important for multiple queries?
    │
    ├─ YES → Use VIEW (persist logic)
    │        Examples:
    │        • Customer totals
    │        • Student averages
    │        • Product summaries
    │
    └─ NO → Use CTE (temporary)
             Examples:
             • One-time analysis
             • Quick calculation
             • Temporary ranking
```

---

## Practical Example: Library System

### Scenario A: One-Time Analysis (Use CTE)

**Task:** Find books borrowed most this month (one-time report)

```sql
-- CTE: Perfect for one-time use
WITH MonthlyBorrows AS (
    SELECT 
        book_id,
        COUNT(*) as borrow_count
    FROM Loans
    WHERE MONTH(loan_date) = MONTH(GETDATE())
    GROUP BY book_id
)
SELECT 
    b.title,
    mb.borrow_count
FROM Books b
JOIN MonthlyBorrows mb ON b.book_id = mb.book_id
ORDER BY borrow_count DESC;
```

**Why CTE?**
- Used only once
- Monthly report
- Not needed permanently

---

### Scenario B: Regular Use (Use VIEW)

**Task:** Track available books (needed daily by many librarians)

```sql
-- VIEW: Perfect for repeated use
CREATE VIEW AvailableBooks AS
SELECT 
    b.book_id,
    b.title,
    b.author,
    b.total_copies,
    COUNT(l.loan_id) as currently_borrowed,
    (b.total_copies - COUNT(l.loan_id)) as available_now
FROM Books b
LEFT JOIN Loans l ON b.book_id = l.book_id 
    AND l.return_date IS NULL
GROUP BY b.book_id, b.title, b.author, b.total_copies;

-- Used daily by everyone (Easy!)
SELECT * FROM AvailableBooks WHERE available_now > 0;
SELECT * FROM AvailableBooks WHERE title LIKE '%Math%';
SELECT * FROM AvailableBooks ORDER BY available_now;
```

**Why VIEW?**
- Used every day
- Multiple librarians need it
- Important logic to persist

---

## Summary Table

| Aspect | CTE | VIEW |
|--------|-----|------|
| **Scope** | One query | Multiple queries |
| **Persistence** | Temporary | Permanent |
| **Lifespan** | Query execution | Until dropped |
| **Maintenance** | None (auto cleanup) | CREATE, ALTER, DROP |
| **Persists** | Nothing | Logic |
| **Use when** | One-time need | Repeated use |
| **Cleanup** | Automatic | Manual |
| **Ease of use** | Easier | Needs management |

---

## Real-World Analogy

### CTE = Scratch Paper
- Use for quick calculations
- Throw away after solving problem
- No permanent storage
- Easy and quick

### VIEW = Reference Book
- Keep for long-term use
- Everyone can access it
- Stored on library shelf
- Needs cataloging

---

## Key Takeaways

📌 **CTE** = Reduce redundancy WITHIN one query  
📌 **VIEW** = Reduce redundancy ACROSS many queries  
📌 **CTE** = Temporary, auto-deleted  
📌 **VIEW** = Persistent, manually managed  
📌 **Tables** = Persist DATA  
📌 **Views** = Persist LOGIC  
📌 **CTEs** = Persist NOTHING  
📌 **Use CTE** = One-time, not important project-wide  
📌 **Use VIEW** = Repeated use, important logic  

**Remember:** If the logic is important enough to share with the whole project, make it a VIEW. If it's just for one query, use a CTE! 🎯
