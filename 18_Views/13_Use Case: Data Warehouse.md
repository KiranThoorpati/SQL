# SQL Views: Building Virtual Data Marts in a Data Warehouse

## Understanding with a Simple Example

Imagine your school has information scattered everywhere:
- **Attendance records** in the office
- **Exam scores** with teachers
- **Library books** in the library system
- **Sports achievements** with the PE department

Now, the principal wants to create ONE central record room (Data Warehouse) with all this information. Then, different people need different subsets:
- **Teachers** need student performance data
- **Sports coach** needs fitness and sports data
- **Principal** needs overall statistics

Instead of making physical copies for each person (which means updating multiple places), we create **"windows"** (views) that show specific information from the central room. This is what virtual data marts do!

---

## What is a Data Warehouse?

A **Data Warehouse** is like a big storage room where data from many sources is collected, cleaned, and organized in one place.

A **Data Mart** is a smaller, focused section that shows specific information for specific purposes.

---

## Why Use Views for Data Marts?

### ❌ **Physical Data Marts (Using Tables)**
- Creates duplicate copies of data
- Need to update multiple places
- Takes up more storage space
- Hard to maintain

### ✅ **Virtual Data Marts (Using Views)**
- No duplicate data
- Updates automatically
- Saves storage space
- Easy to change and maintain

---

## Practical Example: School Data Warehouse

### Step 1: Create the Central Data Warehouse (Physical Tables)

```sql
-- Main warehouse table: Student Information
CREATE TABLE student_data (
    student_id INT,
    student_name VARCHAR(50),
    class VARCHAR(10),
    math_score INT,
    science_score INT,
    attendance_percent INT,
    sports_participation VARCHAR(20),
    library_books_issued INT
);

-- Insert sample data
INSERT INTO student_data VALUES
(1, 'Rahul Sharma', '7A', 85, 90, 95, 'Cricket', 5),
(2, 'Priya Patel', '7A', 92, 88, 98, 'Badminton', 8),
(3, 'Amit Kumar', '7B', 78, 82, 85, 'Football', 3),
(4, 'Sneha Reddy', '7B', 95, 94, 92, 'Basketball', 6),
(5, 'Rohan Verma', '7C', 88, 86, 90, 'Cricket', 4);
```

**Output when we check the warehouse:**
```sql
SELECT * FROM student_data;
```

```
student_id | student_name  | class | math_score | science_score | attendance_percent | sports_participation | library_books_issued
-----------|---------------|-------|------------|---------------|-------------------|---------------------|--------------------
1          | Rahul Sharma  | 7A    | 85         | 90            | 95                | Cricket             | 5
2          | Priya Patel   | 7A    | 92         | 88            | 98                | Badminton           | 8
3          | Amit Kumar    | 7B    | 78         | 82            | 85                | Football            | 3
4          | Sneha Reddy   | 7B    | 95         | 94            | 92                | Basketball          | 6
5          | Rohan Verma   | 7C    | 88         | 86            | 90                | Cricket             | 4
```

---

### Step 2: Create Virtual Data Marts (Views)

#### **Data Mart 1: Academic Performance (For Teachers)**

```sql
-- View showing only academic information
CREATE VIEW academic_mart AS
SELECT 
    student_id,
    student_name,
    class,
    math_score,
    science_score,
    (math_score + science_score) / 2 AS average_score,
    CASE 
        WHEN (math_score + science_score) / 2 >= 90 THEN 'Excellent'
        WHEN (math_score + science_score) / 2 >= 75 THEN 'Good'
        ELSE 'Needs Improvement'
    END AS performance_category
FROM student_data;
```

**Teachers can now query:**
```sql
SELECT * FROM academic_mart;
```

**Output:**
```
student_id | student_name  | class | math_score | science_score | average_score | performance_category
-----------|---------------|-------|------------|---------------|---------------|--------------------
1          | Rahul Sharma  | 7A    | 85         | 90            | 87            | Good
2          | Priya Patel   | 7A    | 92         | 88            | 90            | Excellent
3          | Amit Kumar    | 7B    | 78         | 82            | 80            | Good
4          | Sneha Reddy   | 7B    | 95         | 94            | 94            | Excellent
5          | Rohan Verma   | 7C    | 88         | 86            | 87            | Good
```

---

#### **Data Mart 2: Sports & Activities (For Sports Coach)**

```sql
-- View showing only sports and activity information
CREATE VIEW sports_mart AS
SELECT 
    student_id,
    student_name,
    class,
    sports_participation,
    attendance_percent,
    CASE 
        WHEN attendance_percent >= 95 THEN 'Regular'
        WHEN attendance_percent >= 85 THEN 'Good'
        ELSE 'Irregular'
    END AS attendance_status
FROM student_data
WHERE sports_participation IS NOT NULL;
```

**Sports coach can query:**
```sql
SELECT * FROM sports_mart;
```

**Output:**
```
student_id | student_name  | class | sports_participation | attendance_percent | attendance_status
-----------|---------------|-------|---------------------|-------------------|------------------
1          | Rahul Sharma  | 7A    | Cricket             | 95                | Regular
2          | Priya Patel   | 7A    | Badminton           | 98                | Regular
3          | Amit Kumar    | 7B    | Football            | 85                | Good
4          | Sneha Reddy   | 7B    | Basketball          | 92                | Good
5          | Rohan Verma   | 7C    | Cricket             | 90                | Good
```

---

#### **Data Mart 3: Library Usage (For Librarian)**

```sql
-- View showing only library information
CREATE VIEW library_mart AS
SELECT 
    student_id,
    student_name,
    class,
    library_books_issued,
    CASE 
        WHEN library_books_issued >= 6 THEN 'Avid Reader'
        WHEN library_books_issued >= 4 THEN 'Regular Reader'
        ELSE 'Occasional Reader'
    END AS reader_category
FROM student_data
ORDER BY library_books_issued DESC;
```

**Librarian can query:**
```sql
SELECT * FROM library_mart;
```

**Output:**
```
student_id | student_name  | class | library_books_issued | reader_category
-----------|---------------|-------|---------------------|------------------
2          | Priya Patel   | 7A    | 8                   | Avid Reader
4          | Sneha Reddy   | 7B    | 6                   | Avid Reader
1          | Rahul Sharma  | 7A    | 5                   | Regular Reader
5          | Rohan Verma   | 7C    | 4                   | Regular Reader
3          | Amit Kumar    | 7B    | 3                   | Occasional Reader
```

---

### Step 3: See the Magic - Automatic Updates!

```sql
-- Add a new student to the warehouse
INSERT INTO student_data VALUES
(6, 'Ananya Singh', '7C', 96, 98, 99, 'Tennis', 7);

-- Now check all data marts automatically have the new student!
```

**Check Academic Mart:**
```sql
SELECT * FROM academic_mart WHERE student_id = 6;
```
**Output:**
```
student_id | student_name   | class | math_score | science_score | average_score | performance_category
-----------|----------------|-------|------------|---------------|---------------|--------------------
6          | Ananya Singh   | 7C    | 96         | 98            | 97            | Excellent
```

**Check Sports Mart:**
```sql
SELECT * FROM sports_mart WHERE student_id = 6;
```
**Output:**
```
student_id | student_name   | class | sports_participation | attendance_percent | attendance_status
-----------|----------------|-------|---------------------|-------------------|------------------
6          | Ananya Singh   | 7C    | Tennis              | 99                | Regular
```

**Check Library Mart:**
```sql
SELECT * FROM library_mart WHERE student_id = 6;
```
**Output:**
```
student_id | student_name   | class | library_books_issued | reader_category
-----------|----------------|-------|---------------------|------------------
6          | Ananya Singh   | 7C    | 7                   | Avid Reader
```

---

## Benefits of Virtual Data Marts

| Feature | Physical Tables | Virtual Views |
|---------|----------------|---------------|
| **Storage** | Takes lots of space | Minimal space |
| **Updates** | Manual sync needed | Automatic |
| **Maintenance** | Complex | Simple |
| **Flexibility** | Hard to change | Easy to modify |
| **Data Consistency** | Can go out of sync | Always in sync |

---

## Summary

**Virtual Data Marts** using views are like smart windows that:
1. Show only what you need to see
2. Update automatically when main data changes
3. Save storage space
4. Keep everything organized and consistent

This is why views are PERFECT for building data marts in data warehouses! You maintain data in ONE place (the warehouse) and create different "views" for different purposes - just like having one source of truth with multiple perspectives.
