# Understanding SQL NO JOIN (Querying Multiple Tables Separately)

## What is NO JOIN?

**NO JOIN** means getting data from multiple tables **WITHOUT combining them**. You simply want to see data from different tables in **separate results**.

Think of it like reading two different books - you read them separately, not mixing the pages together!

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/edfdca2f-c821-4a11-95a3-5e20caa681ec" />

---

## When to Use NO JOIN?

Use NO JOIN when:
- You want to see data from multiple tables
- You DON'T need to connect or combine the data
- You just want separate results for each table

---

## Basic Syntax

```sql
SELECT * FROM table1;
SELECT * FROM table2;
```

**Important:** 
- Use **semicolon (;)** to separate queries
- Each query gives its own result
- NO JOIN keyword is used!

---

## Example 1: School Data

**Table: students**
| StudentID | Name | Class |
|-----------|---------|-------|
| 1 | Rahul | 7A |
| 2 | Priya | 7B |
| 3 | Amit | 7A |

**Table: teachers**
| TeacherID | Name | Subject |
|-----------|---------|---------|
| 101 | Mr. Sharma | Math |
| 102 | Ms. Gupta | Science |

**Task:** Retrieve all data from students and teachers in two different results.

**Query:**
```sql
SELECT * FROM students;
SELECT * FROM teachers;
```

**Expected Output:**

**Result 1 (Students):**
| StudentID | Name | Class |
|-----------|---------|-------|
| 1 | Rahul | 7A |
| 2 | Priya | 7B |
| 3 | Amit | 7A |

**Result 2 (Teachers):**
| TeacherID | Name | Subject |
|-----------|---------|---------|
| 101 | Mr. Sharma | Math |
| 102 | Ms. Gupta | Science |

**Notice:** Two completely separate results! ✅

---

## Example 2: Store Data

**Table: customers**
| CustomerID | Name | City |
|------------|---------|-----------|
| 1 | Arjun | Mumbai |
| 2 | Kavya | Delhi |
| 3 | Rohan | Bangalore |

**Table: orders**
| OrderID | Product | Amount |
|---------|---------|--------|
| 101 | Laptop | 50000 |
| 102 | Phone | 30000 |
| 103 | Tablet | 20000 |

**Task:** Get all customers and all orders separately.

**Query:**
```sql
SELECT * FROM customers;
SELECT * FROM orders;
```

**Expected Output:**

**Result 1 (All Customers):**
| CustomerID | Name | City |
|------------|---------|-----------|
| 1 | Arjun | Mumbai |
| 2 | Kavya | Delhi |
| 3 | Rohan | Bangalore |

**Result 2 (All Orders):**
| OrderID | Product | Amount |
|---------|---------|--------|
| 101 | Laptop | 50000 |
| 102 | Phone | 30000 |
| 103 | Tablet | 20000 |

**See?** No connection between customers and orders! They're completely separate! 📊

---

## Example 3: Library System

**Table: books**
| BookID | Title | Author |
|--------|---------------|-------------|
| 1 | Harry Potter | J.K. Rowling |
| 2 | Matilda | Roald Dahl |
| 3 | The Hobbit | J.R.R. Tolkien |

**Table: members**
| MemberID | Name | JoinDate |
|----------|---------|------------|
| 201 | Sneha | 2024-01-15 |
| 202 | Vikram | 2024-02-20 |
| 203 | Diya | 2024-03-10 |

**Task:** Show all books and all members.

**Query:**
```sql
SELECT * FROM books;
SELECT * FROM members;
```

**Expected Output:**

**Result 1 (All Books):**
| BookID | Title | Author |
|--------|---------------|-------------|
| 1 | Harry Potter | J.K. Rowling |
| 2 | Matilda | Roald Dahl |
| 3 | The Hobbit | J.R.R. Tolkien |

**Result 2 (All Members):**
| MemberID | Name | JoinDate |
|----------|---------|------------|
| 201 | Sneha | 2024-01-15 |
| 202 | Vikram | 2024-02-20 |
| 203 | Diya | 2024-03-10 |

---

## Example 4: Selecting Specific Columns

You don't have to select ALL columns - you can choose specific ones!

**Table: products**
| ProductID | ProductName | Price | Stock |
|-----------|-------------|-------|-------|
| 1 | Notebook | 50 | 100 |
| 2 | Pencil | 10 | 500 |
| 3 | Eraser | 5 | 300 |

**Table: suppliers**
| SupplierID | CompanyName | City |
|------------|---------------|---------|
| 1 | ABC Supplies | Mumbai |
| 2 | XYZ Store | Delhi |

**Task:** Get only product names and prices, and supplier names.

**Query:**
```sql
SELECT ProductName, Price FROM products;
SELECT CompanyName FROM suppliers;
```

**Expected Output:**

**Result 1 (Products):**
| ProductName | Price |
|-------------|-------|
| Notebook | 50 |
| Pencil | 10 |
| Eraser | 5 |

**Result 2 (Suppliers):**
| CompanyName |
|---------------|
| ABC Supplies |
| XYZ Store |

---

## NO JOIN vs JOIN - The Difference

### NO JOIN (Separate Results):
```sql
SELECT * FROM students;
SELECT * FROM marks;
```
**Result:** Two separate tables ✅

### WITH JOIN (Combined Result):
```sql
SELECT students.Name, marks.Subject, marks.Marks
FROM students
INNER JOIN marks
ON students.StudentID = marks.StudentID;
```
**Result:** One combined table with data from both ✅

---

## Visual Comparison

### NO JOIN:
```
Table A        Table B
[Result 1]     [Result 2]
   ↓              ↓
Separate      Separate
  Box            Box
```

### WITH JOIN:
```
Table A  +  Table B
    ↓         ↓
  [Combined Result]
   (One Box)
```

---

## Real-World Example: Sports Data

**Table: cricket_players**
| PlayerID | Name | Role |
|----------|---------|---------|
| 1 | Virat | Batsman |
| 2 | Bumrah | Bowler |
| 3 | Rohit | Batsman |

**Table: football_players**
| PlayerID | Name | Position |
|----------|---------|----------|
| 1 | Messi | Forward |
| 2 | Ronaldo | Forward |

**Task:** Show all cricket players and football players separately.

**Query:**
```sql
SELECT * FROM cricket_players;
SELECT * FROM football_players;
```

**Expected Output:**

**Result 1 (Cricket Players):**
| PlayerID | Name | Role |
|----------|---------|---------|
| 1 | Virat | Batsman |
| 2 | Bumrah | Bowler |
| 3 | Rohit | Batsman |

**Result 2 (Football Players):**
| PlayerID | Name | Position |
|----------|---------|----------|
| 1 | Messi | Forward |
| 2 | Ronaldo | Forward |

**Perfect!** Two different sports, two different results! ⚽🏏

---

## Adding WHERE Clause to NO JOIN

You can filter data in each query separately!

**Table: students**
| StudentID | Name | Class | Marks |
|-----------|---------|-------|-------|
| 1 | Rahul | 7A | 85 |
| 2 | Priya | 7B | 92 |
| 3 | Amit | 7A | 78 |
| 4 | Sneha | 7B | 88 |

**Table: subjects**
| SubjectID | SubjectName | Teacher |
|-----------|-------------|-----------|
| 1 | Math | Mr. Sharma |
| 2 | Science | Ms. Gupta |
| 3 | English | Mr. Verma |

**Task:** Get students from 7A only, and subjects taught by Mr. Sharma only.

**Query:**
```sql
SELECT * FROM students WHERE Class = '7A';
SELECT * FROM subjects WHERE Teacher = 'Mr. Sharma';
```

**Expected Output:**

**Result 1 (7A Students):**
| StudentID | Name | Class | Marks |
|-----------|---------|-------|-------|
| 1 | Rahul | 7A | 85 |
| 3 | Amit | 7A | 78 |

**Result 2 (Mr. Sharma's Subject):**
| SubjectID | SubjectName | Teacher |
|-----------|-------------|-----------|
| 1 | Math | Mr. Sharma |

---

## When NOT to Use NO JOIN

❌ **Don't use NO JOIN when you need to:**
- Connect data from both tables
- See which items match between tables
- Add information from one table to another
- Compare data across tables

✅ **Use JOIN instead** for these scenarios!

---

## Practice Questions

### Question 1:
You have `fruits` and `vegetables` tables. Get all data from both separately.

**Query:**
```sql
SELECT * FROM fruits;
SELECT * FROM vegetables;
```

---

### Question 2:
**Table: boys**
| StudentID | Name |
|-----------|---------|
| 1 | Arjun |
| 2 | Rohan |

**Table: girls**
| StudentID | Name |
|-----------|---------|
| 1 | Priya |
| 2 | Diya |

Show all boys and girls separately.

**Query:**
```sql
SELECT * FROM boys;
SELECT * FROM girls;
```

**Expected Output:**

**Result 1:**
| StudentID | Name |
|-----------|---------|
| 1 | Arjun |
| 2 | Rohan |

**Result 2:**
| StudentID | Name |
|-----------|---------|
| 1 | Priya |
| 2 | Diya |

---

## Quick Summary

| Feature | NO JOIN |
|---------|---------|
| **Combines tables?** | ❌ NO |
| **Number of results** | Multiple (one per table) |
| **Syntax** | Multiple SELECT statements |
| **Uses semicolon?** | ✅ YES (to separate) |
| **Data connected?** | ❌ NO (completely separate) |

---

## Key Points to Remember

✅ NO JOIN = Multiple separate queries

✅ Use semicolon (;) between queries

✅ Each query gives its own result

✅ No connection between the results

✅ Simple SELECT statements only

✅ Great for viewing different tables independently

---

**Remember:** NO JOIN is like looking at two different photo albums - you see both, but they're completely separate! 📸📸

**Next up:** We'll learn INNER JOIN where we actually combine the data! 🔗
