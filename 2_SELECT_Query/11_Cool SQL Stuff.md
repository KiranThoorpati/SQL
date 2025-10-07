# Cool SQL Tips and Tricks

## 1. Running Multiple Queries Together

Did you know you can run **multiple SQL queries at once**? Each query gives its own separate result!

### Syntax
```sql
Query1;
Query2;
Query3;
```

**Important:** Use a **semicolon (;)** at the end of each query to separate them!

---

### Example: Multiple Queries

**Tables:**

**customers**
| ID | Name | Country |
|----|---------|---------|
| 1 | Rahul | India |
| 2 | Priya | India |

**orders**
| OrderID | Product | Amount |
|---------|---------|--------|
| 101 | Laptop | 50000 |
| 102 | Phone | 30000 |

**Query:**
```sql
SELECT * FROM customers;
SELECT * FROM orders;
```

**Expected Output:**

**Result 1 (from first query):**
| ID | Name | Country |
|----|---------|---------|
| 1 | Rahul | India |
| 2 | Priya | India |

**Result 2 (from second query):**
| OrderID | Product | Amount |
|---------|---------|--------|
| 101 | Laptop | 50000 |
| 102 | Phone | 30000 |

You get **two separate result tables**! ✨

---

## 2. Static Values (Your Own Data!)

You can display **your own values** in SQL without using any table!

### Basic Syntax
```sql
SELECT 'your_value' AS column_name;
```

---

### Example 1: Displaying Numbers

**Query:**
```sql
SELECT 123 AS StaticNumber;
```

**Expected Output:**
| StaticNumber |
|--------------|
| 123 |

---

### Example 2: Displaying Text

**Query:**
```sql
SELECT 'Hello' AS StaticString;
```

**Expected Output:**
| StaticString |
|--------------|
| Hello |

---

### Example 3: Multiple Static Values

**Query:**
```sql
SELECT 123 AS StaticNumber;
SELECT 'Hello' AS StaticString;
```

**Expected Output:**

**Result 1:**
| StaticNumber |
|--------------|
| 123 |

**Result 2:**
| StaticString |
|--------------|
| Hello |

---

## 3. Mixing Database Data with Static Values

You can combine **real data from tables** with **your own static values**!

### Example: Adding Customer Type

**Table: customers**
| ID | FirstName | Country |
|----|-----------|---------|
| 1 | Rahul | India |
| 2 | Priya | USA |
| 3 | Amit | UK |

**Query:**
```sql
SELECT 
    ID, 
    FirstName, 
    'New Customer' AS CustomerType
FROM customers;
```

**Expected Output:**
| ID | FirstName | CustomerType |
|----|-----------|--------------|
| 1 | Rahul | New Customer |
| 2 | Priya | New Customer |
| 3 | Amit | New Customer |

**Explanation:** 
- ID and FirstName come from the database
- 'New Customer' is a static value that appears for **every row**!

---

### Real-World Example: School Report

**Table: students**
| StudentID | Name | Marks |
|-----------|---------|-------|
| 1 | Arjun | 85 |
| 2 | Kavya | 92 |
| 3 | Rohan | 78 |

**Query:**
```sql
SELECT 
    StudentID,
    Name,
    Marks,
    'Grade 7' AS Class,
    'Final Exam' AS ExamType
FROM students;
```

**Expected Output:**
| StudentID | Name | Marks | Class | ExamType |
|-----------|---------|-------|-------|-----------|
| 1 | Arjun | 85 | Grade 7 | Final Exam |
| 2 | Kavya | 92 | Grade 7 | Final Exam |
| 3 | Rohan | 78 | Grade 7 | Final Exam |

**Cool, right?** Class and ExamType are the same for everyone because they're static values!

---

## 4. Running Part of Your Query (Highlighting)

Sometimes you have a big query but want to run **only part of it**. You can do this by **highlighting** the part you want!

### Example Scenario

**Full Query in Editor:**
```sql
SELECT * FROM customers WHERE Country = 'India';
SELECT * FROM orders;
```

**What You Can Do:**

### Option 1: Run Only First Query
Highlight only:
```sql
SELECT * FROM customers WHERE Country = 'India';
```
Then press Execute → Only first query runs!

---

### Option 2: Run Without WHERE Clause
From the first query, highlight only:
```sql
SELECT * FROM customers
```
Then press Execute → You get ALL customers (filter is ignored)!

---

### Option 3: Run Second Query Only
Highlight only:
```sql
SELECT * FROM orders;
```
Then press Execute → Only orders table is shown!

---

### Option 4: Run Everything
Don't highlight anything, just press Execute → All queries run together!

---

## Practical Examples

### Example 1: Quick Testing

**Query:**
```sql
-- Test if 10 + 5 = 15
SELECT 10 + 5 AS Result;

-- Test string concatenation
SELECT 'Hello' + ' World' AS Greeting;
```

**Expected Output:**

**Result 1:**
| Result |
|--------|
| 15 |

**Result 2:**
| Greeting |
|-------------|
| Hello World |

---

### Example 2: Creating Sample Data Display

**Query:**
```sql
SELECT 
    'Sample Product' AS ProductName,
    100 AS Price,
    'In Stock' AS Status;
```

**Expected Output:**
| ProductName | Price | Status |
|---------------|-------|----------|
| Sample Product | 100 | In Stock |

---

### Example 3: Combining Multiple Information Sources

**Table: products**
| ProductID | ProductName | Price |
|-----------|-------------|-------|
| 1 | Notebook | 50 |
| 2 | Pencil | 10 |

**Query:**
```sql
SELECT 
    ProductID,
    ProductName,
    Price,
    'Rupees' AS Currency,
    'Available' AS Status,
    2024 AS Year
FROM products;
```

**Expected Output:**
| ProductID | ProductName | Price | Currency | Status | Year |
|-----------|-------------|-------|----------|-----------|------|
| 1 | Notebook | 50 | Rupees | Available | 2024 |
| 2 | Pencil | 10 | Rupees | Available | 2024 |

---

## Why These Features Are Useful

### ✅ Multiple Queries
- Get different reports at once
- Compare different tables side-by-side
- Save time by running everything together

### ✅ Static Values
- Add labels or categories to your data
- Test calculations without needing tables
- Create sample reports quickly

### ✅ Mixing Data Types
- Add extra information to reports
- Label data for better understanding
- Create professional-looking outputs

### ✅ Highlighting Parts
- Test pieces of complex queries
- Debug problems step by step
- Work faster without deleting code

---

## Practice Questions

### Question 1: Multiple Queries
Write two queries: one to show all students, another to show all subjects.

```sql
SELECT * FROM students;
SELECT * FROM subjects;
```

---

### Question 2: Static Values
Display your name, age, and grade as static values.

```sql
SELECT 
    'Arjun' AS Name,
    13 AS Age,
    '7th' AS Grade;
```

**Expected Output:**
| Name | Age | Grade |
|-------|-----|-------|
| Arjun | 13 | 7th |

---

### Question 3: Mixed Data
From a books table, add 'Library' as Location and 'Available' as Status.

**Table: books**
| BookID | Title |
|--------|------------|
| 1 | Harry Potter |
| 2 | Mathematics |

```sql
SELECT 
    BookID,
    Title,
    'Library' AS Location,
    'Available' AS Status
FROM books;
```

**Expected Output:**
| BookID | Title | Location | Status |
|--------|------------|----------|-----------|
| 1 | Harry Potter | Library | Available |
| 2 | Mathematics | Library | Available |

---

## Quick Summary Table

| Feature | What It Does | Example |
|---------|--------------|---------|
| **Multiple Queries** | Run many queries at once | `SELECT...; SELECT...;` |
| **Static Values** | Show your own data | `SELECT 'Hello' AS Msg;` |
| **Mixed Data** | Combine table + static | `SELECT Name, 'Active' FROM...` |
| **Highlighting** | Run only selected part | Highlight & Execute |

---

## Important Tips

💡 **Tip 1:** Always use semicolon (;) between multiple queries

💡 **Tip 2:** Static values appear the same in every row

💡 **Tip 3:** You can mix as many static columns as you want

💡 **Tip 4:** Highlighting helps you test and debug faster

💡 **Tip 5:** Static values don't need FROM clause

---

**Remember:** These features make SQL more flexible and fun to use! Practice them to become a SQL expert! 🚀
