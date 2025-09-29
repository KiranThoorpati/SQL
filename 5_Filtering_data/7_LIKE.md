## Class Notes: LIKE Operator - Searching for Patterns in Text

**Note**: This transcript is about the LIKE operator, which is part of SQL's WHERE clause filtering (intermediate SQL), not DDL. DDL deals with table structure (CREATE, ALTER, DROP), while this is about searching within data.

---

### 1. What is the LIKE Operator?

**LIKE** is an operator used to **search for patterns in text**. Think of it like using "Find" in a word processor - you can search for words that start with, end with, or contain certain letters!

**When to use LIKE**:
- Searching for names that start with a certain letter
- Finding words that contain specific characters
- Looking for text patterns anywhere in a column

---

### 2. Two Special Pattern Characters

To build search patterns, SQL uses two special symbols:

| Symbol | Name | What it means | Example |
|--------|------|---------------|---------|
| **%** | Percentage | Anything (zero, one, or many characters) | Most commonly used |
| **_** | Underscore | Exactly one character (no more, no less) | Rarely used |

---

### 3. Basic LIKE Syntax

```sql
SELECT * FROM table_name
WHERE column_name LIKE 'pattern'
```

**Breaking it down**:
1. `WHERE column_name` - Which column to search in
2. `LIKE` - The search operator
3. `'pattern'` - The pattern using % and _

---

### 4. The % (Percentage) Symbol - "Anything Goes"

**What % means**:
- Zero characters (nothing) ✓
- One character ✓
- Many characters ✓
- Numbers, letters, spaces - anything! ✓

---

### 5. Pattern Example 1: Starts With 'M'

**Pattern**: `'M%'`
**Meaning**: Must start with M, then anything after

**Test Values**:
```
'Maria'   → ✓ Starts with M, followed by aria
'Ma'      → ✓ Starts with M, followed by a
'M'       → ✓ Starts with M, nothing after (that's OK!)
'Emma'    → ✗ Starts with E, not M
```

**SQL Example**:
```sql
SELECT * FROM customers
WHERE first_name LIKE 'M%'
```
*Result*: Maria, Martin, Mike, etc.

---

### 6. Pattern Example 2: Ends With 'in'

**Pattern**: `'%in'`
**Meaning**: Can start with anything, but must end with 'in'

**Test Values**:
```
'Martin'  → ✓ Starts with Mart, ends with in
'Vin'     → ✓ Starts with V, ends with in
'in'      → ✓ Starts with nothing, ends with in
'Jasmine' → ✗ Ends with 'ne', not 'in'
```

**SQL Example**:
```sql
SELECT * FROM customers
WHERE first_name LIKE '%in'
```
*Result*: Martin, Justin, Kevin, etc.

---

### 7. Pattern Example 3: Contains 'R' Anywhere

**Pattern**: `'%R%'`
**Meaning**: 'R' can be anywhere - beginning, middle, or end

**Test Values**:
```
'Maria'   → ✓ R is in the middle (maRia)
'Peter'   → ✓ R is at the end (peteR)
'Ryan'    → ✓ R is at the start (Ryan)
'R'       → ✓ Just R alone
'Alice'   → ✗ No R anywhere
```

**SQL Example**:
```sql
SELECT * FROM customers
WHERE first_name LIKE '%r%'
```
*Result*: Maria, Peter, George, Sarah, etc.

**Note**: This is the MOST COMMON pattern - searching for something anywhere!

---

### 8. The _ (Underscore) Symbol - "Exactly One"

**What _ means**:
- Exactly one character (no more, no less)
- Must have something there
- Can be any letter, number, or symbol

---

### 9. Pattern Example 4: 'B' in Third Position

**Pattern**: `'__B%'`
**Meaning**: 
- Position 1: Exactly one character (anything)
- Position 2: Exactly one character (anything)
- Position 3: Must be 'B'
- After that: Anything or nothing

**Test Values**:
```
'Albert'  → ✓ A, L, then B, then ert
'Rob'     → ✓ R, O, then B, nothing after
'Abe'     → ✗ A, B in position 2 (not 3), then e
'An'      → ✗ Only 2 characters, no third position
```

**SQL Example**:
```sql
SELECT * FROM customers
WHERE first_name LIKE '__r%'
```
*Result*: Maria, Martin (R in 3rd position)

---

### 10. Common LIKE Patterns (Quick Reference)

| Pattern | What it finds | Example matches |
|---------|---------------|-----------------|
| `'M%'` | Starts with M | Maria, Mike, Matt |
| `'%n'` | Ends with n | John, Martin, Ryan |
| `'%ar%'` | Contains 'ar' | Maria, Mark, Sarah |
| `'_a%'` | 'a' in 2nd position | Maria, David, Sam |
| `'__r%'` | 'r' in 3rd position | Maria, Martin |
| `'M%n'` | Starts with M, ends with n | Martin, Morgan |
| `'%'` | Everything (all rows) | Any name |

---

### 11. Practice Examples from Transcript

**Example 1: Names starting with M**
```sql
SELECT * FROM customers
WHERE first_name LIKE 'M%'
```
*Result*: Maria, Martin

**Example 2: Names ending with N**
```sql
SELECT * FROM customers
WHERE first_name LIKE '%n'
```
*Result*: John, Martin

**Example 3: Names containing R**
```sql
SELECT * FROM customers
WHERE first_name LIKE '%r%'
```
*Result*: Maria, George, Martin, Peter

**Example 4: R in third position**
```sql
SELECT * FROM customers
WHERE first_name LIKE '__r%'
```
*Result*: Maria, Martin (not Peter - R is in 4th position)

---

### 12. More Practice Examples

**Example 5: Four-letter names**
```sql
SELECT * FROM students
WHERE name LIKE '____'
-- Four underscores = exactly 4 characters
```
*Result*: John, Mary, Mike

**Example 6: Email addresses from Gmail**
```sql
SELECT * FROM users
WHERE email LIKE '%@gmail.com'
```
*Result*: john@gmail.com, mary@gmail.com

**Example 7: Phone numbers starting with 555**
```sql
SELECT * FROM contacts
WHERE phone LIKE '555%'
```
*Result*: 555-1234, 555-5678

**Example 8: Middle name starts with A**
```sql
SELECT * FROM students
WHERE middle_name LIKE 'A%'
```
*Result*: Anne, Andrew, Alice

---

### 13. Important LIKE Rules

**Rule 1**: LIKE is case-sensitive in some databases
```sql
'M%'   → Finds 'Maria' but might not find 'maria'
```
(Depends on your database settings)

**Rule 2**: Always use single quotes for patterns
```sql
WHERE name LIKE 'M%'     ✓ Correct
WHERE name LIKE "M%"     ✗ Wrong (use single quotes)
WHERE name LIKE M%       ✗ Wrong (needs quotes)
```

**Rule 3**: % can match nothing
```sql
'M%' matches 'M'  ✓ Yes, % can be zero characters
```

**Rule 4**: _ must match exactly one
```sql
'_M' matches 'M'   ✗ No, _ needs one character before M
'_M' matches 'AM'  ✓ Yes, exactly one character before M
```

---

### 14. Common Mistakes

❌ **Mistake 1: Forgetting % symbol**
```sql
WHERE name LIKE 'M'  -- Only finds exactly 'M', nothing else
-- Should be: WHERE name LIKE 'M%'
```

❌ **Mistake 2: Using wrong quotes**
```sql
WHERE name LIKE "Maria"  -- Wrong quote type
-- Should be: WHERE name LIKE 'Maria'
```

❌ **Mistake 3: Confusing _ and %**
```sql
WHERE name LIKE 'M_'  -- Finds 'Ma', 'Mo' (exactly 2 chars)
WHERE name LIKE 'M%'  -- Finds 'M', 'Ma', 'Maria' (any length)
```

❌ **Mistake 4: Wrong underscore count**
```sql
-- Want R in 3rd position:
WHERE name LIKE '_r%'   -- Wrong! R in 2nd position
WHERE name LIKE '__r%'  -- Correct! R in 3rd position
```

---

### 15. LIKE vs Equals (=)

| Feature | = (Equals) | LIKE |
|---------|-----------|------|
| **Purpose** | Exact match | Pattern match |
| **Example** | `name = 'Maria'` | `name LIKE 'M%'` |
| **Finds** | Only exact 'Maria' | Maria, Mike, Matt, etc. |
| **Speed** | Faster | Slower |
| **Use when** | You know exact value | Searching for patterns |

---

### 16. Real-World Search Scenarios

**Scenario 1: Find students whose names start with 'A' or 'B'**
```sql
SELECT * FROM students
WHERE name LIKE 'A%' OR name LIKE 'B%'
```

**Scenario 2: Find email addresses from school**
```sql
SELECT * FROM users
WHERE email LIKE '%@school.edu'
```

**Scenario 3: Find products with 'phone' in name**
```sql
SELECT * FROM products
WHERE product_name LIKE '%phone%'
```
*Finds*: iPhone, smartphone, headphones, phone case

**Scenario 4: Find 5-digit zip codes**
```sql
SELECT * FROM addresses
WHERE zip_code LIKE '_____'
-- Five underscores
```

---

### 17. Combining LIKE with Other Operators

**Example 1: LIKE with AND**
```sql
SELECT * FROM students
WHERE name LIKE 'M%' AND grade = 7
```
*Finds*: Grade 7 students whose names start with M

**Example 2: LIKE with OR**
```sql
SELECT * FROM customers
WHERE name LIKE 'M%' OR name LIKE 'J%'
```
*Finds*: Names starting with M or J

**Example 3: NOT LIKE**
```sql
SELECT * FROM products
WHERE name NOT LIKE '%broken%'
```
*Finds*: Products without 'broken' in name

---

### 18. Key Vocabulary

- **LIKE**: Operator for pattern searching in text
- **Pattern**: A search template using % and _
- **% (Percentage)**: Wildcard for any number of characters
- **_ (Underscore)**: Wildcard for exactly one character
- **Wildcard**: Special symbol that can match multiple values
- **Case-sensitive**: Whether uppercase and lowercase matter

---

### 19. Quick Pattern Building Guide

**Want to find names that...**

| Requirement | Pattern | Example |
|-------------|---------|---------|
| Start with 'A' | `'A%'` | Anna, Alex |
| End with 'y' | `'%y'` | Mary, Bobby |
| Contain 'son' | `'%son%'` | Johnson, Mason |
| Are 4 letters long | `'____'` | John, Sara |
| Second letter is 'a' | `'_a%'` | Sam, David |
| Start with 'M', end with 'n' | `'M%n'` | Martin, Morgan |

---

### 20. Summary

**LIKE Operator**:
- Searches for patterns in text columns
- Uses two special symbols:
  - **%** = anything (zero or more characters)
  - **_** = exactly one character
- Most common pattern: `'%word%'` (find 'word' anywhere)
- Case-sensitive in some databases
- Slower than exact match (=) but more flexible
- Perfect for searching when you don't know exact values

**Remember**: 
- **%** is your friend - use it when you don't care about what comes before or after
- **_** is for precision - use it when position matters
- Always test your patterns with SELECT before using them in UPDATE or DELETE!

This completes your SQL filtering operators - you now know how to use WHERE with comparison, logical, membership (IN), and search (LIKE) operators!
