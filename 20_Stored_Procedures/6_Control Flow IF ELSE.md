# Control Flow with IF-ELSE Statements 🔀

## What is Control Flow?

**Simple Definition:** Control flow means making decisions in your code - telling SQL to do different things based on different situations.

**Think of it like:** A traffic light! 🚦
- **IF** the light is green → GO
- **ELSE** (if it's red) → STOP

---

## The Problem: NULL Values in Data

### What's the Issue?

Looking at our data, some customers have **NULL** in their score column:

| Customer | Score |
|----------|-------|
| John | 850 |
| Sarah | NULL |
| Mike | 720 |

**Why is this bad?**
- NULL means "no value" or "missing data"
- When calculating averages, NULLs mess up the results
- We need to clean the data BEFORE doing calculations

### The Solution:

Replace NULL with 0 before calculating averages!

---

## The Logic We Need

**Step 1:** Check - Do we have any NULL values?
- **IF YES** → Update NULL to 0
- **IF NO** → Skip the update (no cleaning needed)

**Step 2:** Generate reports with clean data

This is called **ETL** (Extract, Transform, Load):
1. **Prepare** the data (clean it)
2. **Generate** reports (use it)

<img width="1919" height="1077" alt="image" src="https://github.com/user-attachments/assets/703a4c50-0c76-475e-908d-ab96699137a6" />

---

## IF-ELSE Syntax (The Rules)

### Basic Structure:

```sql
IF (condition)
BEGIN
    -- Code to run if condition is TRUE
END
ELSE
BEGIN
    -- Code to run if condition is FALSE
END
```

**Breaking it down:**
- `IF` → Check something
- `(condition)` → What you're checking
- First `BEGIN...END` → Runs if TRUE
- `ELSE` → Otherwise...
- Second `BEGIN...END` → Runs if FALSE

---

## Step-by-Step: Building the IF-ELSE Logic

### Step 1: Write a Query to Check for NULLs

```sql
SELECT 1
FROM sales_customers
WHERE score IS NULL
  AND country = 'USA';
```

**What this does:**
- Looks for customers with NULL scores in USA
- **If it finds any:** Returns results (something exists)
- **If it finds none:** Returns nothing (empty)

**Testing:**
- **USA:** Returns results → Has NULLs
- **Germany:** Returns nothing → No NULLs

---

### Step 2: Use EXISTS to Check

`EXISTS` checks if a query returns anything:

```sql
IF EXISTS (
    SELECT 1
    FROM sales_customers
    WHERE score IS NULL
      AND country = @country
)
```

**Means:** "If this query finds anything..."

---

### Step 3: Complete IF-ELSE Block

```sql
-- Check if NULL values exist
IF EXISTS (
    SELECT 1
    FROM sales_customers
    WHERE score IS NULL
      AND country = @country
)
BEGIN
    -- NULL values found - clean them up!
    PRINT 'Updating null scores to zero';
    
    UPDATE sales_customers
    SET score = 0
    WHERE score IS NULL
      AND country = @country;
END
ELSE
BEGIN
    -- No NULL values - nothing to clean!
    PRINT 'No null scores found';
END
```

---

## Complete Stored Procedure with Control Flow

```sql
ALTER PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'
AS
BEGIN
    -- Declare variables
    DECLARE @total_customers INT,
            @average_score FLOAT;
    
    -- ============================================
    -- PART 1: PREPARE AND CLEAN UP DATA
    -- ============================================
    
    -- Check if NULL values exist
    IF EXISTS (
        SELECT 1
        FROM sales_customers
        WHERE score IS NULL
          AND country = @country
    )
    BEGIN
        -- NULL values found - clean them!
        PRINT 'Updating null scores to zero';
        
        UPDATE sales_customers
        SET score = 0
        WHERE score IS NULL
          AND country = @country;
    END
    ELSE
    BEGIN
        -- No NULL values found
        PRINT 'No null scores found';
    END
    
    -- ============================================
    -- PART 2: GENERATE REPORTS
    -- ============================================
    
    -- Assign values to variables
    SELECT @total_customers = COUNT(*),
           @average_score = AVG(score)
    FROM sales_customers
    WHERE country = @country;
    
    -- Print customer statistics
    PRINT 'Total customers from ' + @country + ' is equal to ' + 
          CAST(@total_customers AS NVARCHAR);
    
    PRINT 'Average score from ' + @country + ' is equal to ' + 
          CAST(@average_score AS NVARCHAR);
    
    -- Order statistics
    SELECT COUNT(order_id) AS Total_Orders,
           SUM(sales) AS Total_Sales
    FROM sales_orders
    JOIN sales_customers 
        ON sales_orders.customer_id = sales_customers.customer_id
    WHERE country = @country;
END
```

---

## How It Works - Step by Step

### Executing for USA (has NULL values):

```sql
EXECUTE Get_Customer_Summary;
```

**What happens:**
1. Check: Does USA have NULL scores? → **YES**
2. Execute IF block:
   - Print: "Updating null scores to zero"
   - UPDATE: Change NULL to 0
3. Generate reports with clean data
4. Result: More accurate average!

**Messages output:**
```
Updating null scores to zero
Total customers from USA is equal to 3
Average score from USA is equal to 525
```

---

### Executing for Germany (no NULL values):

```sql
EXECUTE Get_Customer_Summary @country = 'Germany';
```

**What happens:**
1. Check: Does Germany have NULL scores? → **NO**
2. Execute ELSE block:
   - Print: "No null scores found"
   - Skip UPDATE (not needed!)
3. Generate reports
4. Result: No changes made

**Messages output:**
```
No null scores found
Total customers from Germany is equal to 2
Average score from Germany is equal to 425
```

---

## Why Check First? (Important!)

### You might think: "Why not just UPDATE every time?"

```sql
-- Bad approach: Update every time
UPDATE sales_customers
SET score = 0
WHERE score IS NULL AND country = @country;
```

**Why this is BAD:**
- Wastes computer resources
- SQL has to check the entire table every time
- Slows down performance with big tables
- Does unnecessary work even when there's nothing to update

### Better approach: Check first, then update!

```sql
-- Good approach: Check if needed, then update
IF EXISTS (check for NULLs)
    THEN update
ELSE 
    skip
```

**Why this is GOOD:**
- Only updates when necessary
- Saves resources and time
- More professional coding
- Faster performance

---

## Visual Understanding

### The Decision Tree:

```
START
  ↓
Check: Are there NULL values?
  ↓
  ├─ YES ────→ UPDATE table (clean data)
  │             ↓
  └─ NO ─────→ Skip update
                ↓
        Generate Reports
                ↓
              END
```

---

## Important Concepts to Remember

### 1. EXISTS Function

**Purpose:** Check if a query returns any results

```sql
IF EXISTS (SELECT 1 FROM table WHERE condition)
```

- Returns TRUE if query finds something
- Returns FALSE if query finds nothing

### 2. IS NULL

**Purpose:** Check for NULL values

```sql
WHERE score IS NULL  -- Correct
WHERE score = NULL   -- Wrong! This doesn't work!
```

Always use `IS NULL`, not `= NULL`

### 3. WHERE Clause in UPDATE

**CRITICAL:** Always use WHERE when updating!

```sql
-- DANGEROUS - Updates ALL rows!
UPDATE sales_customers
SET score = 0;

-- SAFE - Updates only specific rows
UPDATE sales_customers
SET score = 0
WHERE score IS NULL AND country = @country;
```

---

## Key Parts of Our Stored Procedure

### Part 1: Data Preparation (ETL Transform)
```sql
-- Clean and prepare data
IF EXISTS (check for problems)
    Fix problems
ELSE
    Nothing to fix
```

### Part 2: Report Generation (ETL Load)
```sql
-- Use the clean data
Calculate statistics
Display results
```

This is like a **mini program** - not just simple queries anymore!

---

## Real-World Example

Imagine you're a teacher checking homework:

```sql
-- Check if any student didn't submit homework
IF EXISTS (SELECT * FROM students WHERE homework IS NULL)
BEGIN
    PRINT 'Some students missing homework - sending reminder';
    -- Send reminder email
END
ELSE
BEGIN
    PRINT 'All homework submitted!';
    -- No action needed
END
```

---

## Comparison Table

| Without IF-ELSE | With IF-ELSE |
|----------------|--------------|
| Always updates, even if not needed | Updates only when necessary |
| Wastes resources | Saves resources |
| Slower performance | Faster performance |
| Less professional | More professional |
| Simple but inefficient | Smart and efficient |

---

## Key Takeaways

| Concept | Meaning | Example |
|---------|---------|---------|
| **IF** | Check a condition | `IF EXISTS (...)` |
| **ELSE** | What to do if FALSE | `ELSE BEGIN ... END` |
| **EXISTS** | Does query return anything? | `IF EXISTS (SELECT ...)` |
| **IS NULL** | Check for missing values | `WHERE score IS NULL` |
| **Control Flow** | Making decisions in code | IF this THEN that |
| **ETL** | Prepare data, then use it | Clean → Transform → Report |

---

## Practice Exercise

**Challenge:** Create a stored procedure that checks if a student failed:

```sql
CREATE PROCEDURE Check_Student_Status
    @student_name VARCHAR(50)
AS
BEGIN
    DECLARE @marks INT;
    
    SELECT @marks = marks
    FROM students
    WHERE name = @student_name;
    
    -- Control flow based on marks
    IF @marks < 40
    BEGIN
        PRINT @student_name + ' failed! Marks: ' + CAST(@marks AS NVARCHAR);
        PRINT 'Need to schedule extra classes';
    END
    ELSE
    BEGIN
        PRINT @student_name + ' passed! Marks: ' + CAST(@marks AS NVARCHAR);
        PRINT 'Great job!';
    END
END
```

---

## Important Rules to Remember

1. **Always use BEGIN...END** with IF-ELSE blocks
2. **Use EXISTS** to check if data exists
3. **Use IS NULL**, never = NULL
4. **Always add WHERE** in UPDATE statements
5. **Check first, act second** - saves resources
6. **Clean data before calculations** - more accurate results

**Remember:** IF-ELSE is like giving SQL a brain to make smart decisions!
