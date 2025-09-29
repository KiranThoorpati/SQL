## Class Notes: NOT Operator - Reversing Conditions

**Note**: This transcript is about the NOT operator, which is part of SQL's logical operators used in WHERE clauses (intermediate SQL), not DDL. DDL deals with table structure (CREATE, ALTER, DROP), while this is about filtering data.

---

### 1. What is the NOT Operator?

**NOT** is a logical operator that **reverses or flips the truth** of a condition. Think of it like saying "the opposite of" - if something was true, NOT makes it false, and if it was false, NOT makes it true!

**Simple explanation**:
- Takes one condition and reverses it
- True becomes False
- False becomes True

---

### 2. How NOT is Different from AND/OR

**AND and OR**: Combine TWO conditions
```sql
WHERE age > 10 AND grade = 7  -- TWO conditions
WHERE country = 'USA' OR country = 'UK'  -- TWO conditions
```

**NOT**: Works with ONE condition and reverses it
```sql
WHERE NOT country = 'USA'  -- ONE condition, reversed
```

---

### 3. Basic NOT Syntax

```sql
SELECT * FROM table_name
WHERE NOT condition
```

**Breaking it down**:
1. `WHERE` - Start filtering
2. `NOT` - Reverse the condition
3. `condition` - The condition to reverse

---

### 4. How NOT Works (Step-by-Step Example)

**Original Condition** (without NOT):
```sql
WHERE country = 'USA'
```

**Sample Data**:
```
customers table:
Name   | Country
Maria  | Germany
John   | USA
George | UK
Martin | Germany
Peter  | USA
```

**Without NOT** (normal condition):
```sql
WHERE country = 'USA'
```
Result: John, Peter ✓ (they ARE from USA)

**With NOT** (reversed condition):
```sql
WHERE NOT country = 'USA'
```
Result: Maria, George, Martin ✓ (they are NOT from USA)

---

### 5. Understanding the Reversal

**How NOT checks each row**:

| Customer | Country | country = 'USA'? | NOT (reversed) | In Results? |
|----------|---------|------------------|----------------|-------------|
| Maria | Germany | False | True | ✓ YES |
| John | USA | True | False | ✗ NO |
| George | UK | False | True | ✓ YES |
| Martin | Germany | False | True | ✓ YES |
| Peter | USA | True | False | ✗ NO |

**See the pattern?**
- Customers who DON'T match → Included
- Customers who DO match → Excluded

---

### 6. Example from Transcript

**Task**: Get all customers with score NOT less than 500

**Method 1: Without NOT (direct approach)**
```sql
SELECT * FROM customers
WHERE score >= 500
```
"Score is greater than or equal to 500"

**Method 2: With NOT (reversed approach)**
```sql
SELECT * FROM customers
WHERE NOT score < 500
```
"Score is NOT less than 500"

**Both give the same result!**

---

### 7. NOT with Different Operators

**NOT with Equals (=)**
```sql
WHERE NOT country = 'USA'
-- Same as: WHERE country != 'USA'
-- Same as: WHERE country <> 'USA'
```
Gets all countries EXCEPT USA

**NOT with Greater Than (>)**
```sql
WHERE NOT age > 15
-- Same as: WHERE age <= 15
```
Gets ages 15 and below

**NOT with Less Than (<)**
```sql
WHERE NOT score < 500
-- Same as: WHERE score >= 500
```
Gets scores 500 and above

**NOT with BETWEEN**
```sql
WHERE NOT score BETWEEN 100 AND 500
-- Same as: WHERE score < 100 OR score > 500
```
Gets scores outside the range

**NOT with IN**
```sql
WHERE NOT country IN ('USA', 'UK')
-- Same as: WHERE country NOT IN ('USA', 'UK')
```
Gets countries other than USA and UK

**NOT with LIKE**
```sql
WHERE NOT name LIKE 'M%'
-- Gets names that DON'T start with M
```

---

### 8. NOT vs Alternative Operators

Many times, NOT can be replaced with simpler operators:

| Using NOT | Simpler Alternative | Meaning |
|-----------|-------------------|---------|
| `NOT country = 'USA'` | `country != 'USA'` | Not equal to USA |
| `NOT age > 15` | `age <= 15` | 15 or less |
| `NOT score < 100` | `score >= 100` | 100 or more |
| `NOT status = 'Active'` | `status != 'Active'` | Not active |

**Which is better?**
- Most people prefer the simpler alternative (!=, <=, >=)
- NOT is useful when you want to emphasize the reversal
- Both work exactly the same

---

### 9. Practice Examples

**Example 1: Students NOT in grade 7**
```sql
SELECT * FROM students
WHERE NOT grade = 7

-- Simpler version:
WHERE grade != 7
```

**Example 2: Products NOT expensive**
```sql
SELECT * FROM products
WHERE NOT price > 100

-- Simpler version:
WHERE price <= 100
```

**Example 3: Names NOT starting with 'A'**
```sql
SELECT * FROM customers
WHERE NOT name LIKE 'A%'
```

**Example 4: Scores NOT in passing range**
```sql
SELECT * FROM students
WHERE NOT score BETWEEN 60 AND 100

-- Simpler version:
WHERE score < 60 OR score > 100
```

---

### 10. When NOT is Actually Useful

**Useful Case 1: With complex conditions**
```sql
WHERE NOT (age < 13 AND grade > 7)
-- Reverse a whole complex condition
```

**Useful Case 2: With IN operator**
```sql
WHERE country NOT IN ('USA', 'UK', 'Canada')
-- Clearer than multiple != conditions
```

**Useful Case 3: With EXISTS (advanced)**
```sql
WHERE NOT EXISTS (SELECT ...)
-- Used in advanced queries
```

**Useful Case 4: For emphasis**
```sql
WHERE NOT status = 'Deleted'
-- Emphasizes you want non-deleted items
```

---

### 11. NOT with Parentheses (Complex Conditions)

**Without NOT**:
```sql
WHERE age > 12 AND grade = 7
```
Gets 7th graders older than 12

**With NOT** (reverses the whole thing):
```sql
WHERE NOT (age > 12 AND grade = 7)
```
Gets everyone EXCEPT 7th graders older than 12

**This is the same as**:
```sql
WHERE age <= 12 OR grade != 7
```
(De Morgan's Law - learned in math!)

---

### 12. Common Mistakes

❌ **Mistake 1: Double negatives are confusing**
```sql
WHERE NOT score != 100  -- Confusing!
-- This means score = 100
-- Just write: WHERE score = 100
```

❌ **Mistake 2: Forgetting parentheses with complex conditions**
```sql
WHERE NOT age > 12 AND grade = 7  -- Wrong! NOT only applies to age
-- Should be: WHERE NOT (age > 12 AND grade = 7)
```

❌ **Mistake 3: Using NOT when simpler operator exists**
```sql
WHERE NOT country = 'USA'  -- Works but unnecessarily complex
-- Better: WHERE country != 'USA'
```

❌ **Mistake 4: Confusing NOT with !=**
```sql
-- These are the same:
WHERE NOT country = 'USA'
WHERE country != 'USA'
-- NOT is a logical operator, != is a comparison operator
```

---

### 13. Truth Table for NOT

| Original | NOT (Reversed) |
|----------|---------------|
| True | False |
| False | True |

**Example**:
- If `age > 15` is True → `NOT age > 15` is False
- If `age > 15` is False → `NOT age > 15` is True

---

### 14. NOT with NULL Values

**Important**: NOT behaves specially with NULL!

```sql
WHERE NOT country = 'USA'
```
- Includes: Germany, UK, Canada, etc.
- **Does NOT include**: NULL values
- NULL is neither true nor false, so NOT doesn't include it

To include NULL:
```sql
WHERE NOT country = 'USA' OR country IS NULL
```

---

### 15. Real-World Scenarios

**Scenario 1: Find inactive users**
```sql
SELECT * FROM users
WHERE NOT status = 'Active'

-- Or simpler:
WHERE status != 'Active'
```

**Scenario 2: Exclude certain grades**
```sql
SELECT * FROM students
WHERE NOT grade IN (11, 12)

-- Or:
WHERE grade NOT IN (11, 12)
```

**Scenario 3: Non-urgent orders**
```sql
SELECT * FROM orders
WHERE NOT priority = 'High'

-- Or simpler:
WHERE priority != 'High'
```

**Scenario 4: Books not borrowed**
```sql
SELECT * FROM books
WHERE NOT status = 'Borrowed'
```

---

### 16. Combining NOT with Other Logical Operators

**NOT with AND**:
```sql
WHERE NOT (age < 13 AND grade = 7)
-- Not (young AND in grade 7)
```

**NOT with OR**:
```sql
WHERE NOT (country = 'USA' OR country = 'UK')
-- Not from USA or UK

-- Same as:
WHERE country NOT IN ('USA', 'UK')
```

**NOT with AND and OR**:
```sql
WHERE status = 'Active' AND NOT (age < 13 OR grade > 10)
-- Active students, but not too young or in high grades
```

---

### 17. Key Vocabulary

- **NOT**: Logical operator that reverses a condition
- **Reverse**: Change true to false and false to true
- **Negate**: Another word for reverse or flip
- **Logical operator**: Operator that works with true/false (NOT, AND, OR)
- **Unary operator**: Works with one condition (NOT), unlike binary operators (AND, OR)

---

### 18. Quick Reference

```sql
-- Basic NOT
WHERE NOT condition

-- NOT with equals
WHERE NOT country = 'USA'
-- Better: WHERE country != 'USA'

-- NOT with comparison
WHERE NOT age > 15
-- Better: WHERE age <= 15

-- NOT with BETWEEN
WHERE NOT score BETWEEN 100 AND 500
-- Same as: WHERE score < 100 OR score > 500

-- NOT with IN
WHERE NOT country IN ('USA', 'UK')
-- Or: WHERE country NOT IN ('USA', 'UK')

-- NOT with LIKE
WHERE NOT name LIKE 'M%'

-- NOT with complex conditions
WHERE NOT (condition1 AND condition2)
```

---

### 19. Summary

**NOT Operator**:
- Reverses the truth of a condition
- True becomes False, False becomes True
- Works with ONE condition (unlike AND/OR which combine two)
- Can often be replaced with simpler operators (!=, <=, >=)
- Syntax: `WHERE NOT condition`
- Useful for emphasizing exclusion or with complex conditions
- Be careful with parentheses in complex conditions
- Remember: NOT doesn't include NULL values by default

**Remember**: While NOT works perfectly, often using !=, <=, or >= is clearer and more readable. Use NOT when it makes your intention clearer!

---

### 20. All Logical Operators Complete!

You've now learned all three logical operators:

1. **AND** - Both conditions must be true
2. **OR** - At least one condition must be true  
3. **NOT** - Reverses the condition

These can be combined to create powerful filters:
```sql
WHERE (age >= 12 AND age <= 15) 
  AND grade = 7 
  AND NOT status = 'Inactive'
```

Great job mastering SQL filtering!
