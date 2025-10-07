# Understanding SQL HAVING Clause

## What is HAVING?

**HAVING** is a special filter in SQL that works **after grouping data**. Think of it like this:
- **WHERE** filters data BEFORE grouping
- **HAVING** filters data AFTER grouping

You can only use HAVING when you use GROUP BY!

---

## Basic Syntax

```sql
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE condition
GROUP BY column_name
HAVING aggregate_condition;
```

---

## Example 1: Simple HAVING Clause

**Scenario:** We have a students table with scores from different countries.

**Table: students**
| ID | Name | Country | Score |
|----|---------|---------|-------|
| 1 | Alice | USA | 450 |
| 2 | Bob | UK | 380 |
| 3 | Charlie | USA | 470 |
| 4 | David | Germany | 420 |
| 5 | Emma | Germany | 430 |
| 6 | Frank | UK | 370 |

**Task:** Find countries where total score is greater than 800.

```sql
SELECT Country, SUM(Score) AS TotalScore
FROM students
GROUP BY Country
HAVING SUM(Score) > 800;
```

**Expected Output:**
| Country | TotalScore |
|---------|------------|
| USA | 920 |
| Germany | 850 |

**Explanation:** UK is not shown because 380 + 370 = 750, which is less than 800.

---

## Example 2: WHERE vs HAVING

**Task:** Find total scores by country, but only include students with scores above 400, and show only countries with total score above 800.

```sql
SELECT Country, SUM(Score) AS TotalScore
FROM students
WHERE Score > 400
GROUP BY Country
HAVING SUM(Score) > 800;
```

**Step-by-step execution:**

**Step 1:** WHERE filters individual rows
| ID | Name | Country | Score |
|----|---------|---------|-------|
| 1 | Alice | USA | 450 |
| 3 | Charlie | USA | 470 |
| 4 | David | Germany | 420 |
| 5 | Emma | Germany | 430 |

**Step 2:** GROUP BY combines rows by country
| Country | TotalScore |
|---------|------------|
| USA | 920 |
| Germany | 850 |

**Step 3:** HAVING filters the grouped results

**Expected Output:**
| Country | TotalScore |
|---------|------------|
| USA | 920 |
| Germany | 850 |

---

## Example 3: Using AVERAGE with HAVING

**Task:** Find average score for each country, considering only students with scores not equal to zero, and show only countries with average score greater than 430.

```sql
SELECT Country, AVG(Score) AS AverageScore
FROM students
WHERE Score != 0
GROUP BY Country
HAVING AVG(Score) > 430;
```

**Expected Output:**
| Country | AverageScore |
|---------|--------------|
| USA | 460 |

**Explanation:** 
- WHERE removes any students with score = 0
- GROUP BY calculates average for each country
- HAVING shows only countries where average > 430
- USA average: (450 + 470) / 2 = 460 ✓
- Germany average: (420 + 430) / 2 = 425 ✗

---

## Key Differences: WHERE vs HAVING

| Feature | WHERE | HAVING |
|---------|-------|--------|
| When it works | BEFORE grouping | AFTER grouping |
| What it filters | Individual rows | Grouped results |
| Can use aggregate functions? | No | Yes |
| Position in query | After FROM | After GROUP BY |

---

## Simple Rule to Remember

- Use **WHERE** when filtering original data (like Score > 400)
- Use **HAVING** when filtering grouped data (like SUM(Score) > 800)

---

## Practice Question

**Table: class_marks**
| StudentID | Subject | Marks |
|-----------|---------|-------|
| 1 | Math | 85 |
| 1 | Science | 90 |
| 2 | Math | 75 |
| 2 | Science | 80 |
| 3 | Math | 95 |
| 3 | Science | 88 |

**Question:** Find students whose total marks are greater than 170.

```sql
SELECT StudentID, SUM(Marks) AS TotalMarks
FROM class_marks
GROUP BY StudentID
HAVING SUM(Marks) > 170;
```

**Expected Output:**
| StudentID | TotalMarks |
|-----------|------------|
| 1 | 175 |
| 3 | 183 |

---

**Remember:** HAVING is like a filter that works on groups, not individual rows!
