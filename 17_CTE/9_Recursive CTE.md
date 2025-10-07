# Recursive CTEs - The Looping Magic!
*When a CTE Calls Itself Again and Again*

## What is a Recursive CTE?

A **Recursive CTE** is like a CTE that can **call itself** multiple times until it finishes a task!

Think of it like climbing stairs:
- You start at step 1
- Then you go to step 2
- Then step 3, 4, 5...
- You keep going until you reach the top!

**Key Difference:**

| Type | Executes | Example |
|------|----------|---------|
| **Non-Recursive CTE** | Once | Normal CTEs we learned |
| **Recursive CTE** | Multiple times (loops!) | Keeps running until done |

---

## How Does It Work?

```
DATABASE → CTE (1st time) → Check condition → Not done? → CTE (2nd time) → Check → Not done? → CTE (3rd time) → Check → Done! → MAIN QUERY
```

**The Process:**
1. CTE executes and creates initial results
2. Checks: "Are we done?"
3. If NO → Execute CTE again (adds more data)
4. Keep looping until condition says "YES, we're done!"
5. Pass final results to main query

---

## When Do We Use Recursive CTEs?

We use them for:
- 📊 **Hierarchical data** (company org charts, family trees)
- 🔢 **Sequences** (generating number lists)
- 🌳 **Tree structures** (folders, categories)
- 📈 **Level-based data** (game levels, school grades)

---

## Recursive CTE Structure

```sql
WITH CTE_Name AS (
    -- Part 1: ANCHOR QUERY (runs once)
    SELECT ... -- Starting point
    
    UNION ALL
    
    -- Part 2: RECURSIVE QUERY (loops)
    SELECT ... -- References itself!
    FROM CTE_Name -- ← Calls itself!
    WHERE condition -- ← Breaking condition
)
SELECT * FROM CTE_Name;
```

**Two Parts:**
1. **Anchor Query** = Starting point (runs ONCE)
2. **Recursive Query** = Looping part (runs MULTIPLE times)

---

## Simple Example 1: Generate Numbers 1 to 10

Let's create a sequence of numbers from 1 to 10!

```sql
WITH NumberSequence AS (
    -- ANCHOR QUERY: Start with 1
    SELECT 1 as num
    
    UNION ALL
    
    -- RECURSIVE QUERY: Keep adding 1
    SELECT num + 1
    FROM NumberSequence
    WHERE num < 10  -- Stop at 10
)
SELECT * FROM NumberSequence;
```

**Expected Output:**
```
num
---
1
2
3
4
5
6
7
8
9
10
```

**What Happened:**
1. **Anchor:** Started with 1
2. **Loop 1:** 1 + 1 = 2
3. **Loop 2:** 2 + 1 = 3
4. **Loop 3:** 3 + 1 = 4
5. ... continues until 10
6. **Stop:** When num = 10 (not < 10 anymore)

---

## How It Executes Step-by-Step

Let's trace through the first few iterations:

**Iteration 0 (Anchor):**
```
Result: [1]
Check: Is 1 < 10? YES → Continue
```

**Iteration 1 (Recursive):**
```
Current: 1
Calculate: 1 + 1 = 2
Result: [1, 2]
Check: Is 2 < 10? YES → Continue
```

**Iteration 2 (Recursive):**
```
Current: 2
Calculate: 2 + 1 = 3
Result: [1, 2, 3]
Check: Is 3 < 10? YES → Continue
```

**... keeps going ...**

**Iteration 9 (Recursive):**
```
Current: 10
Check: Is 10 < 10? NO → STOP!
Final Result: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

---

## Example 2: Counting Down from 20

```sql
WITH Countdown AS (
    -- Start at 20
    SELECT 20 as number
    
    UNION ALL
    
    -- Subtract 1 each time
    SELECT number - 1
    FROM Countdown
    WHERE number > 1  -- Stop at 1
)
SELECT * FROM Countdown;
```

**Expected Output:**
```
number
------
20
19
18
17
...
3
2
1
```

---

## Controlling Recursion Limit

SQL has a default limit of 100 iterations. You can change it:

```sql
WITH NumberSequence AS (
    SELECT 1 as num
    UNION ALL
    SELECT num + 1
    FROM NumberSequence
    WHERE num < 1000  -- Want 1000 numbers
)
SELECT * FROM NumberSequence
OPTION (MAXRECURSION 5000);  -- Allow up to 5000 loops
```

**Without setting limit:** Error after 100 iterations  
**With MAXRECURSION 5000:** Can go up to 5000 iterations

---

## Real-World Example: School Class Hierarchy

### Our Table: Students
```
student_id | name    | mentor_id | level
-----------|---------|-----------|------
1          | Alice   | NULL      | NULL
2          | Bob     | 1         | NULL
3          | Charlie | 1         | NULL
4          | Diana   | 2         | NULL
5          | Emma    | 3         | NULL
```

**Structure:**
- Alice is the class monitor (no mentor)
- Bob and Charlie report to Alice
- Diana reports to Bob
- Emma reports to Charlie

---

### Task: Show Student Hierarchy Levels

```sql
WITH StudentHierarchy AS (
    -- ANCHOR: Start with class monitor (no mentor)
    SELECT 
        student_id,
        name,
        mentor_id,
        1 as level  -- Top level
    FROM students
    WHERE mentor_id IS NULL
    
    UNION ALL
    
    -- RECURSIVE: Find students under each level
    SELECT 
        s.student_id,
        s.name,
        s.mentor_id,
        sh.level + 1  -- Next level down
    FROM students s
    INNER JOIN StudentHierarchy sh 
        ON s.mentor_id = sh.student_id
)
SELECT * FROM StudentHierarchy
ORDER BY level, student_id;
```

**Expected Output:**
```
student_id | name    | mentor_id | level
-----------|---------|-----------|------
1          | Alice   | NULL      | 1
2          | Bob     | 1         | 2
3          | Charlie | 1         | 2
4          | Diana   | 2         | 3
5          | Emma    | 3         | 3
```

**Visual Hierarchy:**
```
        Level 1: Alice (Class Monitor)
                  /    \
        Level 2: Bob    Charlie
                  |        |
        Level 3: Diana   Emma
```

---

## How the Hierarchy Query Works

**Step 1: Anchor Query**
```
Find: Students with no mentor (Alice)
Result: [Alice, Level 1]
```

**Step 2: First Recursion**
```
Find: Students whose mentor_id = 1 (Alice's ID)
Match: Bob and Charlie
Result: [Alice-1, Bob-2, Charlie-2]
```

**Step 3: Second Recursion**
```
Find: Students whose mentor_id = 2 or 3
Match: Diana (mentor=2) and Emma (mentor=3)
Result: [Alice-1, Bob-2, Charlie-2, Diana-3, Emma-3]
```

**Step 4: Third Recursion**
```
Find: Students whose mentor_id = 4 or 5
Match: None found
Result: STOP! We're done.
```

---

## Example 3: Company Employee Hierarchy

### Our Table: Employees
```
employee_id | name    | manager_id
------------|---------|------------
1           | Frank   | NULL
2           | Kevin   | 1
3           | Mary    | 1
4           | Michael | 2
5           | Carol   | 3
```

**Company Structure:**
- Frank = CEO (no manager)
- Kevin and Mary report to Frank
- Michael reports to Kevin
- Carol reports to Mary

---

### Query: Show Employee Levels

```sql
WITH EmployeeHierarchy AS (
    -- ANCHOR: CEO (no manager)
    SELECT 
        employee_id,
        name,
        manager_id,
        1 as level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- RECURSIVE: Find all subordinates
    SELECT 
        e.employee_id,
        e.name,
        e.manager_id,
        eh.level + 1
    FROM employees e
    INNER JOIN EmployeeHierarchy eh 
        ON e.manager_id = eh.employee_id
)
SELECT * FROM EmployeeHierarchy
ORDER BY level, name;
```

**Expected Output:**
```
employee_id | name    | manager_id | level
------------|---------|------------|------
1           | Frank   | NULL       | 1
2           | Kevin   | 1          | 2
3           | Mary    | 1          | 2
4           | Michael | 2          | 3
5           | Carol   | 3          | 3
```

**Org Chart:**
```
Level 1:        Frank (CEO)
                 /    \
Level 2:    Kevin    Mary
              |        |
Level 3:   Michael   Carol
```

---

## Example 4: Generate Multiplication Table

```sql
WITH MultiplicationTable AS (
    -- Start with 5 x 1
    SELECT 5 as number, 1 as multiplier, 5*1 as result
    
    UNION ALL
    
    -- Keep multiplying
    SELECT 
        5,
        multiplier + 1,
        5 * (multiplier + 1)
    FROM MultiplicationTable
    WHERE multiplier < 10
)
SELECT 
    number,
    multiplier,
    result,
    CONCAT(number, ' x ', multiplier, ' = ', result) as equation
FROM MultiplicationTable;
```

**Expected Output:**
```
number | multiplier | result | equation
-------|------------|--------|------------
5      | 1          | 5      | 5 x 1 = 5
5      | 2          | 10     | 5 x 2 = 10
5      | 3          | 15     | 5 x 3 = 15
5      | 4          | 20     | 5 x 4 = 20
5      | 5          | 25     | 5 x 5 = 25
...
5      | 10         | 50     | 5 x 10 = 50
```

---

## Important Rules for Recursive CTEs

### ✅ Must Have:
1. **Anchor Query** - Starting point
2. **UNION ALL** - Connects anchor and recursive parts
3. **Recursive Query** - References itself
4. **Breaking Condition** - WHERE clause to stop loop

### ⚠️ Watch Out For:
- **Infinite loops** - Always have a stopping condition!
- **Default limit** - 100 iterations max (use MAXRECURSION to change)
- **Performance** - Too many loops = slow query

---

## Common Mistakes

### ❌ Mistake 1: No Breaking Condition
```sql
WITH InfiniteLoop AS (
    SELECT 1 as num
    UNION ALL
    SELECT num + 1 FROM InfiniteLoop
    -- Missing WHERE! Will run forever!
)
SELECT * FROM InfiniteLoop;
-- ERROR: Max recursion reached!
```

### ✅ Fixed: Add WHERE Condition
```sql
WITH ProperLoop AS (
    SELECT 1 as num
    UNION ALL
    SELECT num + 1 FROM ProperLoop
    WHERE num < 10  -- Stops at 10
)
SELECT * FROM ProperLoop;
```

---

## Quick Comparison

| Feature | Non-Recursive CTE | Recursive CTE |
|---------|-------------------|---------------|
| Executions | Once | Multiple (loops) |
| References itself | ❌ No | ✅ Yes |
| Has UNION ALL | ❌ No | ✅ Yes |
| Has Anchor Query | ❌ No | ✅ Yes |
| Use for | Simple queries | Hierarchies, sequences |

---

## Key Takeaways

📌 **Recursive CTE** = CTE that calls itself  
📌 **Two parts** = Anchor (start) + Recursive (loop)  
📌 **UNION ALL** = Connects both parts  
📌 **Breaking condition** = WHERE clause to stop  
📌 **Use for hierarchies** = Org charts, family trees  
📌 **Use for sequences** = Number lists, patterns  
📌 **Default limit** = 100 loops (changeable)  

---

## Practice Challenge

Create a recursive CTE that:
1. Starts at 2
2. Multiplies by 2 each time (2, 4, 8, 16...)
3. Stops at 128

**Hint:** Start with 2, then multiply current number by 2!

**Try it yourself!** 🚀
