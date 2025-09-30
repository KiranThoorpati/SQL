# Variables in Stored Procedures - Storing Values! 💾

## What is a Variable?

**Simple Definition:** A variable is like a storage box inside your stored procedure where you can keep a value and use it later!

**Think of it like:** A backpack! 🎒
- You put something in it (a book, lunch, pencil)
- Carry it around
- Use it whenever you need it!

---

## Variables vs Parameters - What's the Difference?

| **Parameters** | **Variables** |
|----------------|---------------|
| 🚪 Come from OUTSIDE (user input) | 🏠 Live INSIDE the stored procedure |
| 📥 Given by the person executing | 🔧 Created by the developer (you!) |
| Example: `@country = 'USA'` | Example: `@total_customers` |
| User decides the value | Code calculates the value |

**Simple way to remember:**
- **Parameter** = What you ORDER at a restaurant (you choose)
- **Variable** = Ingredients in the KITCHEN (chef uses them)

---

## Why Do We Need Variables?

### **The New Task:**
Instead of showing results in a table, create a text message like:

```
Total customers from Germany is equal to 2
Average score from Germany is equal to 425
```

**Problem:** How do we get those numbers (2 and 425) into the text?

**Solution:** Use VARIABLES to store the numbers, then put them in the message! 🎯

---

## Working with Variables - 3 Simple Steps

### **Step 1: DECLARE (Tell SQL about your variables)**
### **Step 2: ASSIGN (Give them values)**
### **Step 3: USE (Put them to work!)**

Let's do each step! 👇

---

## Step 1: DECLARE Variables 📢

**Declare = Announcing:** "Hey SQL! I'm going to use these storage boxes!"

```sql
DECLARE @total_customers INT,
        @average_score FLOAT;
```

**Breaking it down:**
- `DECLARE` = Tell SQL you're creating variables
- `@total_customers` = Name of first variable (starts with @)
- `INT` = Data type (integer - whole numbers)
- `@average_score` = Name of second variable
- `FLOAT` = Data type (decimal numbers)

**Where to declare?**
Always at the **START** of your stored procedure, right after `BEGIN`!

```sql
CREATE PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'
AS
BEGIN
    -- Declare variables HERE at the top!
    DECLARE @total_customers INT,
            @average_score FLOAT;
    
    -- Rest of your code...
END
```

---

## Step 2: ASSIGN Values to Variables 💪

**Assign = Filling up the storage boxes with values!**

### **Getting Values from a Query:**

```sql
SELECT @total_customers = COUNT(*),
       @average_score = AVG(score)
FROM sales_customers
WHERE country = @country;
```

**What's happening here:**
- `@total_customers = COUNT(*)` → Count customers, store in variable
- `@average_score = AVG(score)` → Calculate average, store in variable
- **IMPORTANT:** No aliases (AS Total_Customers) when using variables!

### **Before (Normal Query with Aliases):**
```sql
SELECT COUNT(*) AS Total_Customers,
       AVG(score) AS Average_Score
FROM sales_customers
WHERE country = @country;
```
This shows results in a table ✅

### **After (Query Storing in Variables):**
```sql
SELECT @total_customers = COUNT(*),
       @average_score = AVG(score)
FROM sales_customers
WHERE country = @country;
```
This stores values in variables, NO table output! 📦

---

## Step 3: USE Variables 🎨

Now that variables have values, use them anywhere!

### **Using PRINT to Show Messages:**

```sql
PRINT 'Total customers from ' + @country + ' is equal to ' + @total_customers;
PRINT 'Average score from ' + @country + ' is equal to ' + @average_score;
```

**Wait! There's a problem!** 😱

---

## Important: CAST (Converting Data Types) 🔄

**The Problem:**
- PRINT only works with **TEXT** (strings)
- `@total_customers` is an **INTEGER**
- `@average_score` is a **FLOAT**
- You can't mix text and numbers directly!

**The Solution: CAST**

CAST = Convert one data type to another!

```sql
CAST(@total_customers AS NVARCHAR)
```

**Means:** Change the integer to text!

### **Correct Way with CAST:**

```sql
PRINT 'Total customers from ' + @country + ' is equal to ' + 
      CAST(@total_customers AS NVARCHAR);

PRINT 'Average score from ' + @country + ' is equal to ' + 
      CAST(@average_score AS NVARCHAR);
```

---

## Complete Working Example 🎯

```sql
ALTER PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'
AS
BEGIN
    -- Step 1: DECLARE variables
    DECLARE @total_customers INT,
            @average_score FLOAT;
    
    -- Step 2: ASSIGN values to variables
    SELECT @total_customers = COUNT(*),
           @average_score = AVG(score)
    FROM sales_customers
    WHERE country = @country;
    
    -- Step 3: USE variables in PRINT statements
    PRINT 'Total customers from ' + @country + ' is equal to ' + 
          CAST(@total_customers AS NVARCHAR);
    
    PRINT 'Average score from ' + @country + ' is equal to ' + 
          CAST(@average_score AS NVARCHAR);
    
    -- Second query (still returns table results)
    SELECT COUNT(order_id) AS Total_Orders,
           SUM(sales) AS Total_Sales
    FROM sales_orders
    JOIN sales_customers 
        ON sales_orders.customer_id = sales_customers.customer_id
    WHERE country = @country;
END
```

---

## Executing and Seeing Results

### **Execute for USA:**
```sql
EXECUTE Get_Customer_Summary;
```

**Output:**
- **Messages tab:** 
  - Total customers from USA is equal to 3
  - Average score from USA is equal to 825
- **Results tab:** One table showing orders and sales

### **Execute for Germany:**
```sql
EXECUTE Get_Customer_Summary @country = 'Germany';
```

**Output:**
- **Messages tab:**
  - Total customers from Germany is equal to 2
  - Average score from Germany is equal to 425
- **Results tab:** One table showing orders and sales

---

## String Concatenation with + 🔗

**Concatenation = Joining text pieces together**

```sql
'Hello' + ' ' + 'World'
```
**Result:** Hello World

### **Building Dynamic Messages:**

```sql
'Total customers from ' + @country + ' is equal to ' + CAST(@total_customers AS NVARCHAR)
```

**Breaks down to:**
1. `'Total customers from '` → text
2. `+` → join
3. `@country` → parameter value (e.g., 'USA')
4. `+` → join
5. `' is equal to '` → text
6. `+` → join
7. `CAST(@total_customers AS NVARCHAR)` → converted number

**Final result:** Total customers from USA is equal to 3

---

## Common Data Types for Variables 📊

| Data Type | What It Stores | Example |
|-----------|----------------|---------|
| `INT` | Whole numbers | 5, 100, -3 |
| `FLOAT` | Decimal numbers | 3.14, 425.5 |
| `VARCHAR(50)` | Text (up to 50 characters) | 'USA', 'Germany' |
| `NVARCHAR(100)` | Unicode text | 'Hello', 'مرحبا' |
| `DATE` | Dates | 2025-09-30 |

---

## Variables Can Be Used Anywhere! 🌟

```sql
-- In WHERE clauses
WHERE customer_id = @saved_id;

-- In calculations
SET @total = @price * @quantity;

-- Passing values between queries
SELECT @max_score = MAX(score) FROM students;
SELECT * FROM students WHERE score = @max_score;

-- In PRINT statements
PRINT 'The winner is ' + @winner_name;
```

---

## The 3 Steps - Visual Summary 🎨

```
Step 1: DECLARE
📦 Create empty boxes
DECLARE @total_customers INT;

        ⬇️

Step 2: ASSIGN
📦➡️🔢 Fill boxes with values
SELECT @total_customers = COUNT(*) FROM...

        ⬇️

Step 3: USE
📦➡️📄 Use values anywhere
PRINT 'Total: ' + CAST(@total_customers AS NVARCHAR);
```

---

## Key Takeaways 🎓

| Concept | Meaning | Example |
|---------|---------|---------|
| **Variable** | Storage box for values | `@total_customers` |
| **DECLARE** | Create the variable | `DECLARE @name INT` |
| **ASSIGN** | Give it a value | `SELECT @name = COUNT(*)` |
| **CAST** | Convert data type | `CAST(@number AS NVARCHAR)` |
| **Concatenation (+)** | Join text pieces | `'Hello' + ' World'` |
| **PRINT** | Show message | `PRINT 'Hello!'` |

---

## Practice Exercise 🎮

**Challenge:** Create a stored procedure that calculates a student's grade status!

```sql
CREATE PROCEDURE Check_Student_Grade
    @student_name VARCHAR(50)
AS
BEGIN
    -- Declare variable
    DECLARE @marks INT;
    
    -- Assign value
    SELECT @marks = marks
    FROM students
    WHERE name = @student_name;
    
    -- Use variable
    PRINT @student_name + ' scored ' + CAST(@marks AS NVARCHAR) + ' marks!';
END
```

---

## Important Rules to Remember 📌

1. **Always DECLARE first** - at the top after BEGIN
2. **No aliases when assigning** - `SELECT @var = value` (not AS)
3. **CAST numbers to text** - for PRINT statements
4. **Use + for concatenation** - joining text pieces
5. **Variables start with @** - like `@my_variable`

---

**Remember:** Variables are your storage boxes! Declare them, fill them up, and use them wherever you need! 📦✨
