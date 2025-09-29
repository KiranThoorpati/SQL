## Class Notes: DELETE Command - Removing Data from Tables

### 1. What is DELETE Command?

**DELETE** is a DML command used to **remove existing rows** from a table. Think of it like erasing lines from your notebook - the notebook (table) stays, but the information is gone!

**Key Differences**:
- **INSERT** = Add new rows
- **UPDATE** = Change existing rows
- **DELETE** = Remove existing rows
- **DROP** = Remove entire table (DDL command)

---

### 2. Basic DELETE Syntax

```sql
DELETE FROM table_name
WHERE condition
```

**Breaking it down**:
1. `DELETE FROM` - Command to remove data
2. `table_name` - Which table to delete from
3. `WHERE condition` - Which rows to delete (CRITICAL!)

---

### 3. ⚠️ MOST CRITICAL WARNING: Always Use WHERE!

**Without WHERE** (EXTREMELY DANGEROUS!):
```sql
DELETE FROM customers
```
**Result**: ALL customers are deleted! Table becomes empty!

**With WHERE** (SAFE):
```sql
DELETE FROM customers
WHERE ID = 6
```
**Result**: Only customer #6 is deleted

**Rule**: DELETE is MORE DANGEROUS than UPDATE! Always use WHERE unless you really want to delete everything!

---

### 4. Example 1: Delete Multiple Rows

**Task**: Delete all customers with ID greater than 5.

**Step 1 - Check what will be deleted**:
```sql
SELECT * FROM customers
WHERE ID > 5
```
*Result*: Shows 5 customers (IDs: 6, 7, 8, 9, 10)

**Step 2 - Write the DELETE**:
```sql
DELETE FROM customers
WHERE ID > 5
```

**Step 3 - Execute and check message**:
- Message: "5 rows affected" ✓ Good!
- If it said "10 rows affected" ✗ Something's wrong!

**Step 4 - Verify the deletion**:
```sql
SELECT * FROM customers
```
*Result*: Only customers 1-5 remain, customers 6-10 are gone

---

### 5. Safe DELETE Process (Step-by-Step)

**Always follow these steps**:

**Step 1**: Use SELECT to see what will be deleted
```sql
SELECT * FROM customers
WHERE ID > 5  -- Your WHERE condition
```

**Step 2**: Check the results carefully
- Are these the rows you want to delete?
- How many rows appear?
- Are you SURE you want to delete these?

**Step 3**: If results are correct, write DELETE
```sql
DELETE FROM customers
WHERE ID > 5  -- Same WHERE condition!
```

**Step 4**: Execute and check the message
- "5 rows affected" = 5 rows deleted
- Make sure the number matches what you expected

**Step 5**: Verify with SELECT
```sql
SELECT * FROM customers
```
Check that the correct rows are gone

---

### 6. TRUNCATE Command - Deleting All Data Faster

**Two ways to delete ALL data from a table**:

**Method 1: DELETE (Slower)**
```sql
DELETE FROM persons
```
- Deletes all rows one by one
- Keeps logs and records
- Shows "X rows affected"
- SLOW for large tables

**Method 2: TRUNCATE (Faster)**
```sql
TRUNCATE TABLE persons
```
- Deletes all rows at once
- No logs or records
- No "rows affected" message
- VERY FAST for large tables

**When to use each**:
- Use DELETE if you need to keep logs or undo later
- Use TRUNCATE if you just want an empty table quickly

---

### 7. Important: DELETE vs DROP vs TRUNCATE

| Command | What it removes | Table still exists? | Can undo? |
|---------|----------------|-------------------|-----------|
| **DELETE** | Rows (data) | ✓ Yes | Sometimes |
| **TRUNCATE** | All rows (data) | ✓ Yes | No |
| **DROP** | Entire table | ✗ No | No |

**Visual Example**:

**Before** (Table with data):
```
customers table:
ID | name  | country
1  | John  | USA
2  | Mary  | UK
3  | Bob   | Canada
```

**After DELETE FROM customers WHERE ID = 2**:
```
customers table:
ID | name  | country
1  | John  | USA
3  | Bob   | Canada
```
*Table exists, Mary is gone*

**After TRUNCATE TABLE customers**:
```
customers table:
(empty - no rows)
```
*Table exists, all data is gone*

**After DROP TABLE customers**:
```
(nothing - table doesn't exist anymore)
```
*Table is completely gone*

---

### 8. Common DELETE Patterns

**Pattern 1: Delete one specific row**
```sql
DELETE FROM students
WHERE ID = 5
```

**Pattern 2: Delete multiple rows matching condition**
```sql
DELETE FROM students
WHERE grade < 5  -- Delete all failing students
```

**Pattern 3: Delete based on date**
```sql
DELETE FROM orders
WHERE order_date < '2024-01-01'  -- Delete old orders
```

**Pattern 4: Delete NULL values**
```sql
DELETE FROM customers
WHERE email IS NULL  -- Delete customers without email
```

**Pattern 5: Delete all data (keep table)**
```sql
TRUNCATE TABLE temp_data
```

---

### 9. Understanding "Rows Affected" Messages

After DELETE, you get a message:

| Message | Meaning | Is it Correct? |
|---------|---------|----------------|
| "1 row affected" | Deleted 1 row | ✓ If you wanted to delete 1 |
| "5 rows affected" | Deleted 5 rows | ✓ If you expected 5 |
| "0 rows affected" | Deleted nothing | ⚠️ Maybe WHERE didn't match anything |
| "100 rows affected" | Deleted 100 rows | ⚠️ Are you sure this is right? |
| No message | Used TRUNCATE | ⚠️ ALL data deleted |

---

### 10. Common DELETE Mistakes

❌ **Mistake 1: Forgetting WHERE clause**
```sql
DELETE FROM customers
-- DISASTER: Deletes ALL customers!
```

❌ **Mistake 2: Wrong WHERE condition**
```sql
DELETE FROM customers
WHERE ID = 99  -- Customer 99 doesn't exist
-- Result: "0 rows affected" (nothing deleted)
```

❌ **Mistake 3: Using = NULL instead of IS NULL**
```sql
DELETE FROM customers
WHERE country = NULL  -- Wrong! Won't work!
-- Should be: WHERE country IS NULL
```

❌ **Mistake 4: Deleting when you meant to UPDATE**
```sql
-- Wrong: Deletes the customer
DELETE FROM customers WHERE ID = 5

-- Right: Changes the customer's score
UPDATE customers SET score = 0 WHERE ID = 5
```

❌ **Mistake 5: Not testing with SELECT first**
```sql
DELETE FROM customers WHERE grade > 5
-- Did you check which customers have grade > 5?
-- Always SELECT first!
```

---

### 11. Practice Examples

**Example 1: Delete one student**
```sql
-- Check first
SELECT * FROM students WHERE ID = 10

-- Delete
DELETE FROM students WHERE ID = 10
```

**Example 2: Delete inactive users**
```sql
-- Check first
SELECT * FROM users WHERE status = 'Inactive'

-- Delete
DELETE FROM users WHERE status = 'Inactive'
```

**Example 3: Delete old records**
```sql
-- Check first
SELECT * FROM logs WHERE date < '2024-01-01'

-- Delete
DELETE FROM logs WHERE date < '2024-01-01'
```

**Example 4: Delete NULL values**
```sql
-- Check first
SELECT * FROM customers WHERE phone IS NULL

-- Delete
DELETE FROM customers WHERE phone IS NULL
```

**Example 5: Delete all data from table**
```sql
-- Fast way
TRUNCATE TABLE temp_data

-- Or slow way
DELETE FROM temp_data
```

---

### 12. Real-World DELETE Scenarios

**Scenario 1: Remove cancelled orders**
```sql
DELETE FROM orders
WHERE status = 'Cancelled'
```

**Scenario 2: Clean up test data**
```sql
DELETE FROM users
WHERE email LIKE '%@test.com'
```

**Scenario 3: Remove duplicates (advanced)**
```sql
DELETE FROM products
WHERE ID NOT IN (SELECT MIN(ID) FROM products GROUP BY name)
```

**Scenario 4: Delete expired subscriptions**
```sql
DELETE FROM subscriptions
WHERE end_date < '2024-01-01'
```

---

### 13. Comparing All DML Commands

| Command | What it does | Creates rows? | Changes rows? | Removes rows? |
|---------|-------------|---------------|---------------|---------------|
| **INSERT** | Adds new data | ✓ | ✗ | ✗ |
| **UPDATE** | Changes existing data | ✗ | ✓ | ✗ |
| **DELETE** | Removes data | ✗ | ✗ | ✓ |

---

### 14. DELETE Best Practices

✓ **DO**:
- **Always** test with SELECT first
- Use WHERE clause (unless deleting all data intentionally)
- Check "rows affected" message carefully
- Verify with SELECT after DELETE
- Use TRUNCATE for deleting all data from large tables
- Make backups before major deletions

✗ **DON'T**:
- Delete without WHERE (unless you mean to delete everything)
- Skip testing your WHERE condition
- Use = NULL (use IS NULL instead)
- Forget to check how many rows were deleted
- Delete important data without backup

---

### 15. Risk Levels of Commands

**From Safest to Most Dangerous**:

1. **SELECT** - Just looks at data (Safe) 🟢
2. **INSERT** - Adds new data (Low risk) 🟢
3. **UPDATE** - Changes data (Medium risk) 🟡
4. **DELETE** - Removes data (High risk) 🔴
5. **TRUNCATE** - Removes all data (Very high risk) 🔴
6. **DROP** - Removes entire table (Extreme risk) 🔴

---

### 16. Key Vocabulary

- **DELETE**: Command to remove rows from a table
- **TRUNCATE**: Command to quickly remove all rows from a table
- **Rows affected**: How many rows were deleted
- **WHERE clause**: Specifies which rows to delete
- **IS NULL**: Checks if value is NULL
- **DML (Data Manipulation Language)**: Commands that change data (INSERT, UPDATE, DELETE)

---

### 17. Quick Reference Guide

```sql
-- Delete specific rows
DELETE FROM table_name
WHERE condition

-- Delete rows with NULL
DELETE FROM table_name
WHERE column IS NULL

-- Delete all data (slow)
DELETE FROM table_name

-- Delete all data (fast)
TRUNCATE TABLE table_name

-- Always test first!
SELECT * FROM table_name
WHERE condition  -- Same condition as DELETE
```

---

### 18. Complete DML Summary

**You've now learned all three DML commands**:

**INSERT** - Add new rows
```sql
INSERT INTO students (ID, name, grade)
VALUES (1, 'John', 7)
```

**UPDATE** - Change existing rows
```sql
UPDATE students
SET grade = 8
WHERE ID = 1
```

**DELETE** - Remove rows
```sql
DELETE FROM students
WHERE ID = 1
```

---

### 19. Summary

**DELETE Command**:
- Removes existing rows from a table (doesn't remove the table itself)
- Uses WHERE to specify which rows to delete
- **MUST** use WHERE or ALL rows will be deleted!
- Can delete one or multiple rows
- Always returns "X rows affected" message
- **MORE DANGEROUS** than UPDATE - be extra careful!
- Test with SELECT first - this is critical!

**TRUNCATE Command**:
- Quickly removes ALL rows from a table
- Faster than DELETE for large tables
- No "rows affected" message
- Cannot be undone
- Table structure remains

**Remember**: DELETE is the most dangerous DML command - always test with SELECT first, and always use WHERE unless you really want to delete everything!

---

### 20. What's Next?

You've completed the **basics of SQL**:
- **DDL** (Data Definition Language): CREATE, ALTER, DROP - change structure
- **DML** (Data Manipulation Language): INSERT, UPDATE, DELETE - change data

Next, you'll learn **intermediate SQL** topics like:
- Advanced filtering with WHERE clause
- More operators for complex conditions
- Joining multiple tables together

Great job learning the fundamentals! 🎓
