## Class Notes: BETWEEN Operator - Checking if Values Fall Within a Range

**Note**: This transcript is about the BETWEEN operator, which is part of SQL's WHERE clause filtering (intermediate SQL), not DDL. DDL deals with table structure (CREATE, ALTER, DROP), while this is about filtering data based on ranges.

---

### 1. What is the BETWEEN Operator?

**BETWEEN** is an operator used to check if a value **falls within a specific range**. Think of it like checking if a test score falls between 60 and 100 (passing range)!

**When to use BETWEEN**:
- Checking if ages are between 10-15
- Finding prices between $20-$50
- Searching for dates within a time period
- Any situation where you have a minimum and maximum value

---

### 2. Understanding Ranges

To create a range, you need **two boundaries**:

| Part | Name | Example |
|------|------|---------|
| **Lower boundary** | Minimum value | 100 |
| **Upper boundary** | Maximum value | 500 |
| **Range** | Everything between them | 100 to 500 |

**Visual Example**:
```
    100 ←──────── Range ────────→ 500
     |                              |
  Lower                          Upper
 Boundary                      Boundary
```

---

### 3. IMPORTANT: BETWEEN is Inclusive!

**Inclusive** means the boundary values themselves are INCLUDED in the range.

```
BETWEEN 100 AND 500 means:
✓ 100 is included (exactly at lower boundary)
✓ 150 is included (inside range)
✓ 300 is included (inside range)
✓ 500 is included (exactly at upper boundary)
✗ 99 is NOT included (below range)
✗ 501 is NOT included (above range)
```

---

### 4. Basic BETWEEN Syntax

```sql
SELECT * FROM table_name
WHERE column_name BETWEEN lower_value AND upper_value
```

**Breaking it down**:
1. `WHERE column_name` - Which column to check
2. `BETWEEN` - The range operator
3. `lower_value` - Starting point (minimum)
4. `AND` - Connects the two boundaries
5. `upper_value` - Ending point (maximum)

---

### 5. How BETWEEN Works (Step-by-Step Example)

**Range**: BETWEEN 100 AND 500

**Sample Data**:
```
customers table:
Name   | Score
Maria  | 300
John   | 900
George | 750
Martin | 500
Peter  | 0
```

**Checking each row**:

1. **Maria (300)**: Is 300 between 100 and 500?
   - 300 > 100 ✓
   - 300 < 500 ✓
   - **Result**: ✓ INCLUDED (inside range)

2. **John (900)**: Is 900 between 100 and 500?
   - 900 > 500 ✗
   - **Result**: ✗ EXCLUDED (above range)

3. **George (750)**: Is 750 between 100 and 500?
   - 750 > 500 ✗
   - **Result**: ✗ EXCLUDED (above range)

4. **Martin (500)**: Is 500 between 100 and 500?
   - 500 = 500 ✓ (exactly at boundary, inclusive!)
   - **Result**: ✓ INCLUDED (at upper boundary)

5. **Peter (0)**: Is 0 between 100 and 500?
   - 0 < 100 ✗
   - **Result**: ✗ EXCLUDED (below range)

**Final Result**: Only Maria and Martin

---

### 6. Example from Transcript

**Task**: Get all customers whose score is between 100 and 500.

**Method 1: Using BETWEEN**
```sql
SELECT * FROM customers
WHERE score BETWEEN 100 AND 500
```

**Result**:
```
Name   | Score
Maria  | 300
Martin | 500
```

---

### 7. Alternative Method: Using Comparison Operators

You can achieve the same result without BETWEEN by using comparison operators with AND:

**Method 2: Using >= and <=**
```sql
SELECT * FROM customers
WHERE score >= 100 AND score <= 500
```

**This means**:
- `score >= 100` - Score must be greater than or equal to 100
- `AND` - Both conditions must be true
- `score <= 500` - Score must be less than or equal to 500

**Result**: Same as Method 1 (Maria and Martin)

---

### 8. BETWEEN vs Comparison Operators

| Feature | BETWEEN | Comparison Operators |
|---------|---------|---------------------|
| **Readability** | Shorter, cleaner | Longer, more explicit |
| **Clarity** | Need to remember it's inclusive | Clear with >= and <= |
| **Which is better?** | Personal preference | Instructor prefers this |

**Instructor's Opinion**:
- Prefers `score >= 100 AND score <= 500`
- Reason: Can see clearly that boundaries are inclusive (uses `=`)
- Easy to forget if BETWEEN is inclusive or exclusive

**Your Choice**: Both work the same - use whichever you find clearer!

---

### 9. Common BETWEEN Patterns

**Pattern 1: Number ranges (scores, ages, prices)**
```sql
WHERE age BETWEEN 10 AND 15
WHERE price BETWEEN 20 AND 50
WHERE quantity BETWEEN 1 AND 100
```

**Pattern 2: Date ranges**
```sql
WHERE birth_date BETWEEN '2000-01-01' AND '2010-12-31'
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31'
```

**Pattern 3: Negative to positive ranges**
```sql
WHERE temperature BETWEEN -10 AND 30
WHERE balance BETWEEN -500 AND 1000
```

---

### 10. Practice Examples

**Example 1: Students in grades 7-9**
```sql
SELECT * FROM students
WHERE grade BETWEEN 7 AND 9
```
*Gets students in grades 7, 8, and 9*

**Example 2: Products priced $10-$50**
```sql
SELECT * FROM products
WHERE price BETWEEN 10 AND 50
```
*Gets products costing between $10 and $50 (inclusive)*

**Example 3: Ages 12-14**
```sql
SELECT * FROM students
WHERE age BETWEEN 12 AND 14
```
*Gets 12, 13, and 14 year olds*

**Example 4: Scores not in passing range**
```sql
SELECT * FROM students
WHERE score NOT BETWEEN 60 AND 100
```
*Gets students who scored below 60 or above 100*

---

### 11. NOT BETWEEN (The Opposite)

**NOT BETWEEN** finds values OUTSIDE the range:

```sql
SELECT * FROM customers
WHERE score NOT BETWEEN 100 AND 500
```

**This finds**:
- Scores less than 100 (like 0, 50, 99)
- Scores greater than 500 (like 501, 750, 900)

**Same as**:
```sql
WHERE score < 100 OR score > 500
```

---

### 12. Visual Understanding of Ranges

```
Number Line:
    0 ←──────── Outside ────────→ 100 ←──── Inside ────→ 500 ←──── Outside ────→ 1000
    |                              |                        |                      |
  Below                         Lower                    Upper                   Above
  Range                       Boundary                 Boundary                  Range

BETWEEN 100 AND 500 includes the green zone
NOT BETWEEN 100 AND 500 includes the red zones
```

---

### 13. Common Mistakes

❌ **Mistake 1: Wrong order (larger value first)**
```sql
WHERE score BETWEEN 500 AND 100  -- Wrong order!
-- Should be: WHERE score BETWEEN 100 AND 500
```
*Always put smaller value first, larger value second*

❌ **Mistake 2: Forgetting it's inclusive**
```sql
-- If you want 101-499 (excluding boundaries):
WHERE score BETWEEN 100 AND 500  -- Wrong! Includes 100 and 500

-- Should be:
WHERE score > 100 AND score < 500
-- OR
WHERE score BETWEEN 101 AND 499
```

❌ **Mistake 3: Using OR instead of AND**
```sql
WHERE score BETWEEN 100 OR 500  -- Wrong syntax!
-- Should be: WHERE score BETWEEN 100 AND 500
```

❌ **Mistake 4: Missing AND keyword**
```sql
WHERE score BETWEEN 100 500  -- Missing AND!
-- Should be: WHERE score BETWEEN 100 AND 500
```

---

### 14. When to Use Each Method

**Use BETWEEN when**:
- You want shorter code
- Range is simple and clear
- You don't mind memorizing it's inclusive

**Use Comparison Operators (>=, <=) when**:
- You want to be extra clear about inclusiveness
- You need exclusive boundaries (>, <)
- You prefer explicit code

---

### 15. Exclusive vs Inclusive Boundaries

**Inclusive (BETWEEN)**: Includes the boundary values
```sql
WHERE age BETWEEN 10 AND 15
-- Includes: 10, 11, 12, 13, 14, 15
```

**Exclusive**: Does NOT include boundary values
```sql
WHERE age > 10 AND age < 15
-- Includes: 11, 12, 13, 14 (NOT 10 or 15)
```

**Mix (One inclusive, one exclusive)**:
```sql
WHERE age >= 10 AND age < 15
-- Includes: 10, 11, 12, 13, 14 (NOT 15)
```

---

### 16. Real-World Scenarios

**Scenario 1: Find teenagers**
```sql
SELECT * FROM students
WHERE age BETWEEN 13 AND 19
```

**Scenario 2: Mid-priced products**
```sql
SELECT * FROM products
WHERE price BETWEEN 25 AND 75
```

**Scenario 3: This year's orders**
```sql
SELECT * FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31'
```

**Scenario 4: Passing grades**
```sql
SELECT * FROM students
WHERE score BETWEEN 60 AND 100
```

---

### 17. Combining BETWEEN with Other Operators

**Example 1: BETWEEN with AND**
```sql
SELECT * FROM students
WHERE age BETWEEN 12 AND 15
  AND grade = 7
```
*7th graders aged 12-15*

**Example 2: BETWEEN with OR**
```sql
SELECT * FROM products
WHERE price BETWEEN 10 AND 20
   OR price BETWEEN 50 AND 60
```
*Products in two different price ranges*

**Example 3: NOT BETWEEN**
```sql
SELECT * FROM students
WHERE score NOT BETWEEN 0 AND 59
```
*Students who passed (score 60+)*

---

### 18. Key Vocabulary

- **BETWEEN**: Operator that checks if a value is within a range
- **Range**: Area between a minimum and maximum value
- **Boundary**: The edge values of a range (lower and upper)
- **Inclusive**: Includes the boundary values themselves
- **Exclusive**: Does NOT include the boundary values
- **Lower boundary**: Minimum value (starting point)
- **Upper boundary**: Maximum value (ending point)

---

### 19. Quick Reference

```sql
-- Basic BETWEEN
WHERE column BETWEEN lower AND upper

-- Alternative with comparison operators
WHERE column >= lower AND column <= upper

-- NOT BETWEEN (outside range)
WHERE column NOT BETWEEN lower AND upper

-- Alternative for NOT BETWEEN
WHERE column < lower OR column > upper

-- Examples with different data types
WHERE age BETWEEN 10 AND 15           -- Numbers
WHERE name BETWEEN 'A' AND 'M'        -- Text (alphabetical)
WHERE date BETWEEN '2024-01-01' AND '2024-12-31'  -- Dates
```

---

### 20. Summary

**BETWEEN Operator**:
- Checks if a value falls within a range
- Requires two boundaries: lower (minimum) and upper (maximum)
- Is **inclusive** - includes both boundary values
- Syntax: `WHERE column BETWEEN lower_value AND upper_value`
- Can be replaced with: `WHERE column >= lower AND column <= upper`
- NOT BETWEEN finds values outside the range
- Always put smaller value first, larger value second
- Works with numbers, dates, and even text (alphabetical order)

**Remember**: BETWEEN is inclusive - if you want to exclude boundaries, use `>` and `<` instead of `>=` and `<=`!
