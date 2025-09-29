## Class Notes: UPDATE Command - Changing Existing Data

### 1. What is UPDATE Command?

**UPDATE** is a DML command used to **change data in existing rows**. Think of it like using an eraser and pencil to correct information that's already written in your notebook!

**Key Difference**:
- **INSERT** = Adding NEW rows (like adding new pages)
- **UPDATE** = Changing EXISTING rows (like correcting what's already written)

---

### 2. Basic UPDATE Syntax

```sql
UPDATE table_name
SET column1 = new_value1, column2 = new_value2
WHERE condition
```

**Breaking it down**:
1. `UPDATE table_name` - Which table to modify
2. `SET column = value` - What to change and to what
3. `WHERE condition` - Which rows to change (VERY IMPORTANT!)

---

### 3. ⚠️ CRITICAL WARNING: Always Use WHERE!

**Without WHERE** (DANGEROUS!):
```sql
UPDATE customers
SET score = 0
```
**Result**: ALL customers get score = 0 (Everyone's score changes!)

**With WHERE** (SAFE):
```sql
UPDATE customers
SET score = 0
WHERE ID = 6
```
**Result**: Only customer #6 gets score = 0

**Rule**: ALWAYS use WHERE unless you really want to change every single row!

---

### 4. Example 1: Updating One Row

**Task**: Change the score of customer 6 to 0.

**Step 1 - Check the data first**:
```sql
SELECT * FROM customers
WHERE ID = 6
```
*Result*: Shows customer 6 with NULL score

**Step 2 - Write the UPDATE**:
```sql
UPDATE customers
SET score = 0
WHERE ID = 6
```

**Step 3 - Verify your WHERE clause**:
Before running UPDATE, test your WHERE:
```sql
SELECT * FROM customers
WHERE ID = 6  -- Same WHERE clause!
```
If this shows the correct row(s), your UPDATE is safe!

**Step 4 - Execute and check result**:
- Message: "1 row affected" ✓ Good!
- If it said "10 rows affected" ✗ Something's wrong!

**Step 5 - Verify the change**:
```sql
SELECT * FROM customers
```
*Result*: Customer 6 now has score = 0 instead of NULL

---

### 5. Example 2: Updating Multiple Columns at Once

**Task**: Change customer 10's score to 0 AND country to UK.

**Before UPDATE**:
- Customer 10 (Sarah): country = NULL, score = NULL

**The UPDATE command**:
```sql
UPDATE customers
SET score = 0, country = 'UK'
WHERE ID = 10
```

**Notice**:
- Use comma (`,`) to separate multiple column updates
- Can update as many columns as needed in one command

**After UPDATE**:
- Customer 10 (Sarah): country = UK, score = 0
- Message: "1 row affected"

---

### 6. Example 3: Updating Multiple Rows

**Task**: Update ALL customers with NULL score by setting their score to 0.

**Step 1 - Find which rows will be affected**:
```sql
SELECT * FROM customers
WHERE score IS NULL
```
*Result*: Shows 2 customers with NULL scores

**Step 2 - Write the UPDATE**:
```sql
UPDATE customers
SET score = 0
WHERE score IS NULL
```

**Important Note**: To check for NULL, use `IS NULL`, not `= NULL`

**Result**:
- Message: "2 rows affected" ✓ Correct!
- Both customers with NULL scores now have score = 0

---

### 7. Special: Working with NULL Values

**What is NULL?**
- NULL means "no data" or "empty"
- NULL is NOT the same as 0 or empty text

**Checking for NULL** (Special syntax):
```sql
-- CORRECT ✓
WHERE score IS NULL

-- WRONG ✗
WHERE score = NULL  -- This won't work!
```

**Example**:
```sql
-- Find all customers without a country
SELECT * FROM customers
WHERE country IS NULL

-- Update them to 'Unknown'
UPDATE customers
SET country = 'Unknown'
WHERE country IS NULL
```

---

### 8. Safe UPDATE Process (Step-by-Step)

**Always follow these steps**:

**Step 1**: Use SELECT to see what will be changed
```sql
SELECT * FROM customers
WHERE ID = 6  -- Your WHERE condition
```

**Step 2**: Check the results
- Does it show the correct rows?
- How many rows appear?

**Step 3**: If results look correct, write UPDATE
```sql
UPDATE customers
SET score = 0
WHERE ID = 6  -- Same WHERE condition!
```

**Step 4**: Execute and check the message
- "1 row affected" = Good if you wanted to change 1 row
- "10 rows affected" = Check if this is correct!

**Step 5**: Verify the changes
```sql
SELECT * FROM customers
WHERE ID = 6
```

---

### 9. Common UPDATE Patterns

**Pattern 1: Update one specific row**
```sql
UPDATE students
SET grade = 8
WHERE ID = 5
```

**Pattern 2: Update multiple columns**
```sql
UPDATE students
SET grade = 8, age = 14
WHERE ID = 5
```

**Pattern 3: Update multiple rows with same condition**
```sql
UPDATE students
SET grade = 8
WHERE age = 13
-- Changes grade for all 13-year-olds
```

**Pattern 4: Replace NULL values**
```sql
UPDATE students
SET phone = 'Unknown'
WHERE phone IS NULL
```

**Pattern 5: Update based on another column**
```sql
UPDATE students
SET status = 'Pass'
WHERE score >= 50
```

---

### 10. Understanding "Rows Affected" Messages

After UPDATE, you get a message:

| Message | Meaning | Is it Good? |
|---------|---------|-------------|
| "1 row affected" | Changed 1 row | ✓ Good if you wanted 1 row |
| "5 rows affected" | Changed 5 rows | ✓ Good if you expected 5 rows |
| "0 rows affected" | Changed nothing | ⚠️ Maybe your WHERE didn't match anything |
| "100 rows affected" | Changed 100 rows | ⚠️ Check if this is what you wanted! |

---

### 11. Common UPDATE Mistakes

❌ **Mistake 1: Forgetting WHERE clause**
```sql
UPDATE customers
SET score = 0
-- DANGER: Updates ALL customers!
```

❌ **Mistake 2: Wrong WHERE condition**
```sql
UPDATE customers
SET score = 0
WHERE ID = 99  -- Customer 99 doesn't exist!
-- Result: "0 rows affected"
```

❌ **Mistake 3: Using = NULL instead of IS NULL**
```sql
UPDATE customers
SET country = 'USA'
WHERE country = NULL  -- Wrong! Won't work!
-- Should be: WHERE country IS NULL
```

❌ **Mistake 4: Forgetting commas between multiple columns**
```sql
UPDATE customers
SET score = 0 country = 'USA'  -- Missing comma!
WHERE ID = 5
```

❌ **Mistake 5: Wrong data type**
```sql
UPDATE customers
SET ID = 'hello'  -- ID is a number, not text!
WHERE ID = 5
```

---

### 12. Practice Examples

**Example 1: Update one student's age**
```sql
-- First, check who you're updating
SELECT * FROM students WHERE ID = 3

-- Then update
UPDATE students
SET age = 14
WHERE ID = 3
```

**Example 2: Update multiple columns**
```sql
UPDATE students
SET grade = 8, age = 14, status = 'Active'
WHERE ID = 3
```

**Example 3: Update multiple students**
```sql
-- Update all grade 7 students to grade 8
UPDATE students
SET grade = 8
WHERE grade = 7
```

**Example 4: Replace NULL values**
```sql
-- Replace all missing emails with 'no-email@school.com'
UPDATE students
SET email = 'no-email@school.com'
WHERE email IS NULL
```

**Example 5: Conditional update**
```sql
-- Set status to 'Pass' for students with score 50 or more
UPDATE students
SET status = 'Pass'
WHERE score >= 50
```

---

### 13. Comparing INSERT vs UPDATE

| Feature | INSERT | UPDATE |
|---------|--------|--------|
| **Purpose** | Add NEW rows | Change EXISTING rows |
| **Creates new rows?** | Yes | No |
| **Changes existing rows?** | No | Yes |
| **Message** | "X rows affected" (inserted) | "X rows affected" (changed) |
| **Example** | Add new student | Change student's grade |

---

### 14. UPDATE Best Practices

✓ **DO**:
- Always test with SELECT first
- Use WHERE clause (unless updating ALL rows intentionally)
- Check "rows affected" message
- Verify changes with SELECT after UPDATE
- Use IS NULL for checking NULL values

✗ **DON'T**:
- Update without WHERE (unless you mean to change everything)
- Skip testing your WHERE condition
- Use = NULL (use IS NULL instead)
- Forget to check how many rows were affected

---

### 15. Real-World UPDATE Scenarios

**Scenario 1: Student passed an exam**
```sql
UPDATE students
SET status = 'Passed', final_grade = 85
WHERE ID = 12
```

**Scenario 2: Price increase for all products**
```sql
UPDATE products
SET price = price * 1.1  -- Increase by 10%
WHERE category = 'Electronics'
```

**Scenario 3: Fix missing information**
```sql
UPDATE employees
SET department = 'Unassigned'
WHERE department IS NULL
```

**Scenario 4: Mark inactive users**
```sql
UPDATE users
SET status = 'Inactive'
WHERE last_login < '2024-01-01'
```

---

### 16. Key Vocabulary

- **UPDATE**: Command to change existing data
- **SET**: Specifies what to change
- **WHERE**: Specifies which rows to change
- **Rows affected**: How many rows were changed
- **IS NULL**: Checks if value is NULL
- **Condition**: Rule to filter which rows to update

---

### 17. Quick Reference Guide

```sql
-- Basic UPDATE template
UPDATE table_name
SET column = new_value
WHERE condition

-- Update multiple columns
UPDATE table_name
SET column1 = value1, column2 = value2
WHERE condition

-- Update based on NULL
UPDATE table_name
SET column = default_value
WHERE column IS NULL

-- Update multiple rows
UPDATE table_name
SET column = value
WHERE column2 > some_value
```

---

### 18. Summary

**UPDATE Command**:
- Changes data in EXISTING rows (doesn't create new rows)
- Uses SET to specify new values
- MUST use WHERE to target specific rows (or risk changing everything!)
- Can update one or multiple columns at once
- Can update one or multiple rows at once
- Always returns "X rows affected" message
- Test with SELECT first to be safe!

**Remember**: UPDATE is powerful but dangerous - always use WHERE and test first!
