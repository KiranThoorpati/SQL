# Handling NULLs Before Math Operations - Simple Notes

## The Problem with NULL in Math Operations

### Rule #1: NULL + Anything = NULL!

When you do math with NULL, you always get NULL as the answer!

---

## Examples with Numbers

### ✅ Normal Addition:
```
1 + 5 = 6
```
**Result:** 6 (normal math)

### ✅ Addition with Zero:
```
0 + 5 = 5
```
**Result:** 5 (still works fine)

### ❌ Addition with NULL:
```
NULL + 5 = NULL
```
**Result:** NULL (problem!)

**Why?** SQL says: "I'm adding 5 to something I don't know, so I don't know the answer either!"

---

## Examples with Text (Strings)

### ✅ Normal Concatenation (joining text):
```
'A' + 'B' = 'AB'
```
**Result:** AB (joined together)

### ✅ Concatenation with Empty String:
```
'' + 'B' = 'B'
```
**Result:** B (empty string has zero characters)

### ❌ Concatenation with NULL:
```
NULL + 'B' = NULL
```
**Result:** NULL (problem!)

**Why?** SQL says: "I'm joining 'B' with something unknown, so the result is unknown!"

---

## Important Warning! ⚠️

**This NULL problem happens with ALL math operators:**
- Addition: NULL + 5 = NULL
- Subtraction: NULL - 5 = NULL
- Multiplication: NULL × 5 = NULL
- Division: NULL ÷ 5 = NULL

**Solution:** Always handle NULLs BEFORE doing math operations!

---

## Real Example: Customer Database

### Task:
1. Create full names by combining first name + last name
2. Add 10 bonus points to each customer's score

### The Data:

| Customer ID | First Name | Last Name | Score |
|------------|-----------|-----------|-------|
| 1 | Joseph | Goldenberg | 600 |
| 2 | Sarah | Smith | 700 |
| 3 | Mary | NULL | 500 |
| 4 | Anna | Johnson | NULL |

---

## Problem 1: Creating Full Names

### ❌ Wrong Way (Without Handling NULL):

```sql
SELECT 
    customer_id,
    first_name,
    last_name,
    first_name + ' ' + last_name AS full_name
FROM customers
```

**Results:**
| Customer ID | First Name | Last Name | Full Name |
|------------|-----------|-----------|-----------|
| 1 | Joseph | Goldenberg | Joseph Goldenberg ✅ |
| 2 | Sarah | Smith | Sarah Smith ✅ |
| 3 | Mary | NULL | NULL ❌ (Lost Mary's name!) |
| 4 | Anna | Johnson | Anna Johnson ✅ |

**Problem:** Mary's full name shows NULL even though we know her first name is "Mary"!

**Why?** Because: `'Mary' + ' ' + NULL = NULL`

---

### ✅ Right Way (With Handling NULL):

```sql
SELECT 
    customer_id,
    first_name,
    last_name,
    first_name + ' ' + COALESCE(last_name, '') AS full_name
FROM customers
```

**What COALESCE does:**
- If last_name is NULL → Replace with empty string `''`
- If last_name exists → Use the actual name

**Results:**
| Customer ID | First Name | Last Name | Full Name |
|------------|-----------|-----------|-----------|
| 1 | Joseph | Goldenberg | Joseph Goldenberg ✅ |
| 2 | Sarah | Smith | Sarah Smith ✅ |
| 3 | Mary | NULL | Mary ✅ (Shows first name!) |
| 4 | Anna | Johnson | Anna Johnson ✅ |

**Success!** Now Mary's name appears correctly!

---

### Other Options for Missing Last Names:

**Option 1: Empty String** (recommended)
```sql
COALESCE(last_name, '')
-- Result for Mary: "Mary"
```

**Option 2: Placeholder Text**
```sql
COALESCE(last_name, 'N/A')
-- Result for Mary: "Mary N/A"
```

---

## Problem 2: Adding Bonus Points

### ❌ Wrong Way (Without Handling NULL):

```sql
SELECT 
    customer_id,
    first_name,
    score,
    score + 10 AS score_with_bonus
FROM customers
```

**Results:**
| Customer ID | First Name | Score | Score with Bonus |
|------------|-----------|-------|-----------------|
| 1 | Joseph | 600 | 610 ✅ |
| 2 | Sarah | 700 | 710 ✅ |
| 3 | Mary | 500 | 510 ✅ |
| 4 | Anna | NULL | NULL ❌ (No bonus!) |

**Problem:** Anna didn't get any bonus points!

**Why?** Because: `NULL + 10 = NULL`

**Not Fair!** Everyone should get 10 bonus points!

---

### ✅ Right Way (With Handling NULL):

```sql
SELECT 
    customer_id,
    first_name,
    score,
    COALESCE(score, 0) + 10 AS score_with_bonus
FROM customers
```

**What COALESCE does:**
- If score is NULL → Replace with 0
- Then add 10 bonus points

**Results:**
| Customer ID | First Name | Score | Score with Bonus |
|------------|-----------|-------|-----------------|
| 1 | Joseph | 600 | 610 ✅ |
| 2 | Sarah | 700 | 710 ✅ |
| 3 | Mary | 500 | 510 ✅ |
| 4 | Anna | NULL | 10 ✅ (Got bonus!) |

**Success!** Everyone gets their 10 bonus points fairly!

---

## Complete Solution Code

```sql
SELECT 
    customer_id,
    first_name,
    last_name,
    score,
    -- Creating full name (handles NULL last name)
    first_name + ' ' + COALESCE(last_name, '') AS full_name,
    -- Adding bonus points (handles NULL score)
    COALESCE(score, 0) + 10 AS score_with_bonus
FROM sales_customers
```

---

## Step-by-Step Process

### When Doing Math Operations:

**Step 1:** Identify which columns might have NULL

**Step 2:** Use COALESCE to replace NULL BEFORE the operation
```sql
COALESCE(column_name, replacement_value)
```

**Step 3:** Then do your math operation
```sql
COALESCE(column_name, 0) + 10
COALESCE(column_name, '') + ' other text'
```

---

## Common Replacements for NULL

| Type of Data | Replace NULL with | Example |
|-------------|------------------|---------|
| **Numbers** | 0 | `COALESCE(score, 0)` |
| **Text** | Empty string `''` | `COALESCE(last_name, '')` |
| **Text** | Placeholder | `COALESCE(last_name, 'Unknown')` |

---

## Practice Examples

### Example 1: Calculate Total Price
```sql
-- Wrong way
SELECT price + tax AS total
-- If tax is NULL → total becomes NULL!

-- Right way
SELECT price + COALESCE(tax, 0) AS total
-- If tax is NULL → treats it as 0
```

### Example 2: Create Email Address
```sql
-- Wrong way
SELECT first_name + '.' + last_name + '@school.com' AS email
-- If last_name is NULL → email becomes NULL!

-- Right way
SELECT first_name + '.' + COALESCE(last_name, 'student') + '@school.com' AS email
-- If last_name is NULL → uses 'student' instead
```

### Example 3: Calculate Discount
```sql
-- Wrong way
SELECT price - discount AS final_price
-- If discount is NULL → final_price becomes NULL!

-- Right way
SELECT price - COALESCE(discount, 0) AS final_price
-- If discount is NULL → treats it as 0 (no discount)
```

---

## Key Takeaways

1. 🎯 **NULL + anything = NULL** (This ruins your calculations!)

2. 🎯 **Always handle NULL BEFORE doing math operations**

3. 🎯 **Use COALESCE to replace NULL** with appropriate values:
   - For numbers → use 0
   - For text → use empty string `''` or placeholder

4. 🎯 **This applies to ALL operators**: +, -, ×, ÷

5. 🎯 **Check your results!** Make sure no one is unfairly getting NULL values

---

## Remember:
**Handle NULLs FIRST, then do Math!** Otherwise, your results will be unexpected and unfair! 🧮✅
