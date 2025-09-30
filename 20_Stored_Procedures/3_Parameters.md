# Parameters in Stored Procedures - Making Things Flexible! 🎯

## What is a Parameter?

**Simple Definition:** A parameter is like a blank space that you can fill in with different information each time you run the stored procedure.

**Think of it like:** A pizza order form where you can choose toppings! 🍕
- The form is the same
- But YOU decide: cheese, pepperoni, or vegetables each time!

---

## Why Do We Need Parameters?

### The Problem Without Parameters 😕

Imagine you need TWO reports:
1. **Report 1:** Total US customers and their average score
2. **Report 2:** Total German customers and their average score

**Without parameters, you'd need:**
- ❌ Two separate stored procedures
- ❌ Almost identical code (just different country names)
- ❌ More work to maintain and update

**This is BAD CODING!** 😞 Repeating the same code is never good!

---

### The Solution: Use Parameters! ✨

**With parameters, you need:**
- ✅ ONE stored procedure
- ✅ Pass different country names when you execute it
- ✅ Flexible, reusable, and professional!

---

## Example: From Static to Dynamic

### **Before (Static - BAD Way):**

```sql
-- Stored Procedure 1 for USA
CREATE PROCEDURE Get_Customer_Summary_USA
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = 'USA'  -- This value is STUCK!
END

-- Stored Procedure 2 for Germany
CREATE PROCEDURE Get_Customer_Summary_Germany
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = 'Germany'  -- This value is STUCK!
END
```

**Problems:**
- Two stored procedures doing almost the same thing
- If you need France, you'd create a THIRD one! 😫

---

### **After (With Parameters - GOOD Way!):**

```sql
CREATE PROCEDURE Get_Customer_Summary
    @country VARCHAR(50)  -- This is our parameter!
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = @country  -- Uses the parameter value!
END
```

**Now you can use it for ANY country!** 🌍

---

## How to Create Parameters - Step by Step

### **Step 1: Define the Parameter**

```sql
CREATE PROCEDURE Get_Customer_Summary
    @country VARCHAR(50)  -- Parameter definition
```

**Breaking it down:**
- `@country` = Name of the parameter (starts with @)
- `VARCHAR(50)` = Data type (text with max 50 characters)
- You can name it anything: @country_name, @location, etc.

### **Step 2: Use the Parameter in Your Query**

```sql
WHERE country = @country
```

You can use `@country` anywhere in your query!

---

## How to Execute with Parameters

### **Passing Values:**

```sql
-- For Germany report:
EXECUTE Get_Customer_Summary @country = 'Germany'

-- For USA report:
EXECUTE Get_Customer_Summary @country = 'USA'

-- For France report:
EXECUTE Get_Customer_Summary @country = 'France'
```

**See the magic?** Same stored procedure, different results! 🎩✨

---

## Updating an Existing Stored Procedure

If your stored procedure already exists, use `ALTER` instead of `CREATE`:

```sql
ALTER PROCEDURE Get_Customer_Summary
    @country VARCHAR(50)
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = @country
END
```

**ALTER** = Update/change an existing stored procedure

---

## Default Values (Super Useful!) 🌟

### What if you use USA reports most often?

You can set a **default value** so you don't have to type it every time!

```sql
CREATE PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'  -- USA is the default!
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = @country
END
```

### **Now you have TWO options:**

**Option 1: Use the default (USA)**
```sql
EXECUTE Get_Customer_Summary
-- Automatically uses USA! No need to type it!
```

**Option 2: Specify a different country**
```sql
EXECUTE Get_Customer_Summary @country = 'Germany'
-- This overrides the default and uses Germany instead
```

---

## Important Rules About Defaults 📋

1. **Default is a backup** - It's used only when you don't provide a value
2. **Your value has priority** - If you pass a value, SQL uses YOUR value, not the default
3. **Default must make sense** - Choose the most commonly used value

---

## Deleting Stored Procedures

Don't need a stored procedure anymore? Delete it!

```sql
DROP PROCEDURE Get_Customer_Summary_Germany
```

`DROP` = Delete/remove the stored procedure

---

## Complete Example with All Concepts

```sql
-- Create stored procedure with parameter and default value
CREATE PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'  -- Parameter with default
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = @country
END

-- Execute with default (USA)
EXECUTE Get_Customer_Summary

-- Execute with Germany
EXECUTE Get_Customer_Summary @country = 'Germany'

-- Execute with France
EXECUTE Get_Customer_Summary @country = 'France'
```

---

## Key Takeaways 🎯

| Concept | What It Means | Example |
|---------|--------------|---------|
| **Parameter** | A blank you fill in | `@country VARCHAR(50)` |
| **Static Value** | Fixed, never changes | `WHERE country = 'USA'` ❌ |
| **Dynamic Value** | Changes based on input | `WHERE country = @country` ✅ |
| **Default Value** | Backup value if nothing provided | `@country VARCHAR(50) = 'USA'` |
| **ALTER** | Update existing procedure | `ALTER PROCEDURE...` |
| **DROP** | Delete a procedure | `DROP PROCEDURE...` |

---

## The Power of Parameters 💪

### **Without Parameters:**
- Need 10 countries? Create 10 stored procedures! 😰

### **With Parameters:**
- Need 10 countries? Use 1 stored procedure 10 different ways! 😎

**Remember:** Good programmers DON'T repeat code. They make it flexible and reusable!

---

## Practice Exercise 🎓

**Challenge:** Create a stored procedure that gets student grades by class name!

```sql
CREATE PROCEDURE Get_Student_Grades
    @class_name VARCHAR(50) = '7th Grade'
AS
BEGIN
    SELECT student_name, grade
    FROM students
    WHERE class = @class_name
END

-- Try it for different classes!
EXECUTE Get_Student_Grades @class_name = '8th Grade'
EXECUTE Get_Student_Grades  -- Uses default: 7th Grade
```

**Parameters make your stored procedures SUPER POWERFUL!** 🚀
