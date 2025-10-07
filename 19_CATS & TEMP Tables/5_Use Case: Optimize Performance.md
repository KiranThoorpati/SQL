# SQL CTAS: Optimizing Performance with Tables

## The Performance Problem

Imagine you're running a race:
- **Using a View** = Running with a heavy backpack (slower, but you always have everything)
- **Using a CTAS Table** = Running without the backpack (faster, but you prepared earlier)

When dealing with **slow, complex queries**, CTAS can dramatically improve performance!

---

## Understanding the Performance Issue

### The Scenario

You have a complex query that:
- Joins multiple large tables
- Performs heavy calculations
- Takes a long time to run (maybe 5-10 seconds or more!)
- Is used frequently by many users

**Problem:** Every user must wait for this slow query to complete each time!

---

## Setup: Create Complex Scenario

Let's simulate a school database with large tables:

```sql
-- Table 1: Student basic info
CREATE TABLE students (
    student_id INT,
    student_name VARCHAR(50),
    class VARCHAR(10),
    section VARCHAR(5)
);

-- Table 2: Exam scores
CREATE TABLE exam_scores (
    student_id INT,
    subject VARCHAR(30),
    marks INT,
    exam_date DATE
);

-- Table 3: Attendance records
CREATE TABLE attendance (
    student_id INT,
    days_present INT,
    days_absent INT,
    month VARCHAR(20)
);

-- Insert sample data
INSERT INTO students VALUES
(1, 'Aarav Kumar', 'Class 7', 'A'),
(2, 'Diya Patel', 'Class 7', 'B'),
(3, 'Rohan Singh', 'Class 7', 'A'),
(4, 'Priya Sharma', 'Class 7', 'C'),
(5, 'Arjun Reddy', 'Class 7', 'B');

INSERT INTO exam_scores VALUES
(1, 'Math', 85, '2025-09-15'),
(1, 'Science', 90, '2025-09-15'),
(2, 'Math', 92, '2025-09-15'),
(2, 'Science', 88, '2025-09-15'),
(3, 'Math', 78, '2025-09-15'),
(3, 'Science', 82, '2025-09-15'),
(4, 'Math', 88, '2025-09-15'),
(4, 'Science', 92, '2025-09-15'),
(5, 'Math', 95, '2025-09-15'),
(5, 'Science', 93, '2025-09-15');

INSERT INTO attendance VALUES
(1, 20, 2, 'September'),
(2, 22, 0, 'September'),
(3, 18, 4, 'September'),
(4, 21, 1, 'September'),
(5, 22, 0, 'September');
```

---

## The Slow Complex Query (Problem)

### Complex Query Joining Multiple Tables

```sql
-- This query is SLOW because it:
-- 1. Joins 3 tables
-- 2. Does multiple calculations
-- 3. Uses CASE statements
-- 4. Calculates averages

SELECT 
    s.student_id,
    s.student_name,
    s.class,
    s.section,
    AVG(e.marks) AS average_marks,
    COUNT(e.subject) AS subjects_taken,
    a.days_present,
    a.days_absent,
    ROUND((a.days_present * 100.0) / (a.days_present + a.days_absent), 2) AS attendance_percentage,
    CASE 
        WHEN AVG(e.marks) >= 90 THEN 'Outstanding'
        WHEN AVG(e.marks) >= 80 THEN 'Excellent'
        WHEN AVG(e.marks) >= 70 THEN 'Good'
        ELSE 'Needs Improvement'
    END AS performance_grade,
    CASE 
        WHEN (a.days_present * 100.0) / (a.days_present + a.days_absent) >= 95 THEN 'Excellent'
        WHEN (a.days_present * 100.0) / (a.days_present + a.days_absent) >= 85 THEN 'Good'
        ELSE 'Poor'
    END AS attendance_grade
FROM students s
JOIN exam_scores e ON s.student_id = e.student_id
JOIN attendance a ON s.student_id = a.student_id
GROUP BY s.student_id, s.student_name, s.class, s.section, 
         a.days_present, a.days_absent;
```

**Output:**
```
student_id | student_name | class   | section | average_marks | subjects_taken | days_present | days_absent | attendance_percentage | performance_grade | attendance_grade
-----------|--------------|---------|---------|---------------|----------------|--------------|-------------|----------------------|-------------------|------------------
1          | Aarav Kumar  | Class 7 | A       | 87.50         | 2              | 20           | 2           | 90.91                | Excellent         | Good
2          | Diya Patel   | Class 7 | B       | 90.00         | 2              | 22           | 0           | 100.00               | Outstanding       | Excellent
3          | Rohan Singh  | Class 7 | A       | 80.00         | 2              | 18           | 4           | 81.82                | Excellent         | Poor
4          | Priya Sharma | Class 7 | C       | 90.00         | 2              | 21           | 1           | 95.45                | Outstanding       | Excellent
5          | Arjun Reddy  | Class 7 | B       | 94.00         | 2              | 22           | 0           | 100.00               | Outstanding       | Excellent
```

**Problem:** This query is executed EVERY TIME a user needs this report!

---

## Solution 1: Create a View (Still Slow)

```sql
CREATE VIEW student_performance_report AS
SELECT 
    s.student_id,
    s.student_name,
    s.class,
    s.section,
    AVG(e.marks) AS average_marks,
    COUNT(e.subject) AS subjects_taken,
    a.days_present,
    a.days_absent,
    ROUND((a.days_present * 100.0) / (a.days_present + a.days_absent), 2) AS attendance_percentage,
    CASE 
        WHEN AVG(e.marks) >= 90 THEN 'Outstanding'
        WHEN AVG(e.marks) >= 80 THEN 'Excellent'
        WHEN AVG(e.marks) >= 70 THEN 'Good'
        ELSE 'Needs Improvement'
    END AS performance_grade
FROM students s
JOIN exam_scores e ON s.student_id = e.student_id
JOIN attendance a ON s.student_id = a.student_id
GROUP BY s.student_id, s.student_name, s.class, s.section, 
         a.days_present, a.days_absent;
```

**Query the view:**
```sql
SELECT * FROM student_performance_report;
```

⚠️ **Still SLOW!** The complex query runs every time someone queries the view.

---

## Solution 2: Use CTAS (MUCH FASTER!)

### Create a Physical Table with Pre-calculated Results

```sql
-- Create table with all calculations done ONCE
CREATE TABLE student_performance_summary AS
SELECT 
    s.student_id,
    s.student_name,
    s.class,
    s.section,
    AVG(e.marks) AS average_marks,
    COUNT(e.subject) AS subjects_taken,
    a.days_present,
    a.days_absent,
    ROUND((a.days_present * 100.0) / (a.days_present + a.days_absent), 2) AS attendance_percentage,
    CASE 
        WHEN AVG(e.marks) >= 90 THEN 'Outstanding'
        WHEN AVG(e.marks) >= 80 THEN 'Excellent'
        WHEN AVG(e.marks) >= 70 THEN 'Good'
        ELSE 'Needs Improvement'
    END AS performance_grade,
    CASE 
        WHEN (a.days_present * 100.0) / (a.days_present + a.days_absent) >= 95 THEN 'Excellent'
        WHEN (a.days_present * 100.0) / (a.days_present + a.days_absent) >= 85 THEN 'Good'
        ELSE 'Poor'
    END AS attendance_grade
FROM students s
JOIN exam_scores e ON s.student_id = e.student_id
JOIN attendance a ON s.student_id = a.student_id
GROUP BY s.student_id, s.student_name, s.class, s.section, 
         a.days_present, a.days_absent;
```

**Now query the table:**
```sql
SELECT * FROM student_performance_summary;
```

**Output (INSTANT!):**
```
student_id | student_name | class   | section | average_marks | subjects_taken | days_present | days_absent | attendance_percentage | performance_grade | attendance_grade
-----------|--------------|---------|---------|---------------|----------------|--------------|-------------|----------------------|-------------------|------------------
1          | Aarav Kumar  | Class 7 | A       | 87.50         | 2              | 20           | 2           | 90.91                | Excellent         | Good
2          | Diya Patel   | Class 7 | B       | 90.00         | 2              | 22           | 0           | 100.00               | Outstanding       | Excellent
3          | Rohan Singh  | Class 7 | A       | 80.00         | 2              | 18           | 4           | 81.82                | Excellent         | Poor
4          | Priya Sharma | Class 7 | C       | 90.00         | 2              | 21           | 1           | 95.45                | Outstanding       | Excellent
5          | Arjun Reddy  | Class 7 | B       | 94.00         | 2              | 22           | 0           | 100.00               | Outstanding       | Excellent
```

✅ **MUCH FASTER!** No joins, no calculations - just reading stored data!

---

## Performance Comparison

### Using View (Slow)
```
User Query → View → Execute Complex Query → Join 3 Tables → Calculate → Return Result
Time: 5-10 seconds (EVERY TIME!)
```

### Using CTAS Table (Fast)
```
User Query → Table → Read Pre-calculated Data → Return Result
Time: 0.1-0.5 seconds (SUPER FAST!)
```

---

## Simple Queries Become Lightning Fast

### Filter for Outstanding Students

**With View (Slow):**
```sql
SELECT * FROM student_performance_report
WHERE performance_grade = 'Outstanding';
```
⏱️ Time: Still runs complex query + filter = SLOW

**With CTAS Table (Fast):**
```sql
SELECT * FROM student_performance_summary
WHERE performance_grade = 'Outstanding';
```
⚡ Time: Just reads stored data = INSTANT!

**Output:**
```
student_id | student_name | class   | section | average_marks | performance_grade | attendance_grade
-----------|--------------|---------|---------|---------------|-------------------|------------------
2          | Diya Patel   | Class 7 | B       | 90.00         | Outstanding       | Excellent
4          | Priya Sharma | Class 7 | C       | 90.00         | Outstanding       | Excellent
5          | Arjun Reddy  | Class 7 | B       | 94.00         | Outstanding       | Excellent
```

---

### Find Students by Section

**With CTAS (Fast):**
```sql
SELECT student_name, average_marks, performance_grade
FROM student_performance_summary
WHERE section = 'B'
ORDER BY average_marks DESC;
```

**Output (INSTANT):**
```
student_name | average_marks | performance_grade
-------------|---------------|-------------------
Arjun Reddy  | 94.00         | Outstanding
Diya Patel   | 90.00         | Outstanding
```

---

## Real-World Example: Monthly Report Cards

### Scenario: Generate report cards for 1000+ students

**Problem:**
- Complex query takes 10 seconds
- 50 teachers need reports simultaneously
- Total time: 50 × 10 = 500 seconds (8+ minutes!)

**Solution with CTAS:**

```sql
-- Run this ONCE at the end of the month
CREATE TABLE monthly_report_cards AS
SELECT 
    s.student_id,
    s.student_name,
    s.class,
    AVG(e.marks) AS average_score,
    CASE 
        WHEN AVG(e.marks) >= 90 THEN 'A+'
        WHEN AVG(e.marks) >= 80 THEN 'A'
        WHEN AVG(e.marks) >= 70 THEN 'B'
        WHEN AVG(e.marks) >= 60 THEN 'C'
        ELSE 'D'
    END AS final_grade,
    CASE 
        WHEN AVG(e.marks) >= 90 THEN 'Promoted'
        WHEN AVG(e.marks) >= 40 THEN 'Promoted'
        ELSE 'Detained'
    END AS promotion_status
FROM students s
JOIN exam_scores e ON s.student_id = e.student_id
GROUP BY s.student_id, s.student_name, s.class;
```

**Now ALL 50 teachers can query instantly:**
```sql
-- Each teacher gets results in 0.1 seconds!
SELECT * FROM monthly_report_cards
WHERE class = 'Class 7'
ORDER BY average_score DESC;
```

**Output:**
```
student_id | student_name | class   | average_score | final_grade | promotion_status
-----------|--------------|---------|---------------|-------------|------------------
5          | Arjun Reddy  | Class 7 | 94.00         | A+          | Promoted
2          | Diya Patel   | Class 7 | 90.00         | A+          | Promoted
4          | Priya Sharma | Class 7 | 90.00         | A+          | Promoted
1          | Aarav Kumar  | Class 7 | 87.50         | A           | Promoted
3          | Rohan Singh  | Class 7 | 80.00         | A           | Promoted
```

**Time saved:** 500 seconds → 5 seconds total! **100× faster!**

---

## When to Refresh the CTAS Table

Remember: CTAS tables don't auto-update. You need to refresh them periodically.

### Option 1: Drop and Recreate

```sql
-- Drop old table
DROP TABLE student_performance_summary;

-- Create fresh table
CREATE TABLE student_performance_summary AS
SELECT ... -- (your complex query here)
```

### Option 2: Schedule Automatic Refresh

You can set up automatic refresh:
- **Daily** at midnight
- **Weekly** on Sundays
- **Monthly** on the 1st
- **After major data updates**

---

## Performance Optimization Strategy

### Decision Flow:

```
Is your query complex and slow?
    ├── NO → Use a VIEW (simpler, always fresh)
    └── YES → 
        ├── Do you need real-time data?
        │   ├── YES → Use a VIEW (accept slower performance)
        │   └── NO → 
        │       └── Can you refresh periodically?
        │           ├── YES → Use CTAS! ✅
        │           └── NO → Use a VIEW
```

---

## Practical Comparison Table

| Aspect | VIEW | CTAS TABLE |
|--------|------|------------|
| **Speed** | Slow (runs query each time) | Fast (pre-calculated) |
| **Data Freshness** | Always current | Needs manual refresh |
| **Storage** | No extra storage | Uses storage |
| **Best For** | Real-time data | Reports, summaries |
| **Maintenance** | Easy (auto-updates) | Requires refresh |
| **Complex Queries** | Runs every time | Runs once |

---

## Summary

**Use CTAS for Performance When:**
- ✅ Query is complex and slow
- ✅ Many users need the same data
- ✅ Data doesn't need to be real-time
- ✅ You can refresh periodically

**Benefits:**
- ⚡ 10-100× faster query performance
- 👥 Better experience for many users
- 💾 Pre-calculated results ready instantly
- 📊 Perfect for reports and dashboards

**Trade-off:**
- 🔄 Need to refresh manually
- 💽 Uses more storage space
- ⏰ Data might be slightly outdated

**Think of it like:**
- **View** = Cooking fresh food each time (slow but fresh)
- **CTAS** = Meal prep on Sunday (fast all week, but not as fresh)

Choose based on whether you value **speed** or **freshness** more!
