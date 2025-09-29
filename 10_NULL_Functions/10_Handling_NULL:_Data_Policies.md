# SQL Notes: Cleaning Data with Standard Policies

## Why Data Cleaning Matters

### The Big Problem:
In real projects, data is often **messy**! You'll find:
- NULL values
- Empty strings
- Blank spaces (evil!)

### What Happens If You Don't Clean Data?
**Bad data → Wrong analysis → Wrong decisions → Big problems!**

**Think of it like:** Cooking with dirty ingredients. Your meal will turn out bad no matter how good your recipe is!

---

## The Solution: Data Policies (Rules)

Before doing ANY analysis, you need to set up **data policies** (rules) about how to handle messy data.

You work with your team to choose ONE policy and stick to it!

---

## Policy 1: Allow NULL and Empty String (Remove Blank Spaces)

### The Rule:
- NULL is OK
- Empty string ('') is OK  
- Blank spaces are NOT OK (remove them!)

### How to Implement:

**Use the TRIM function** - it removes spaces from the left and right sides of text.

```sql
TRIM(Category) AS Policy1
```

### Example:

**Before TRIM:**
| Row | Category | Length |
|-----|----------|--------|
| 1   | 'A'      | 1      |
| 2   | NULL     | NULL   |
| 3   | ''       | 0      |
| 4   | '  '     | 2      |

**After TRIM:**
| Row | Result   | Length |
|-----|----------|--------|
| 1   | 'A'      | 1      |
| 2   | NULL     | NULL   |
| 3   | ''       | 0      |
| 4   | ''       | 0      | ← Spaces removed!

### What TRIM Does:
```
'  Hello  ' → 'Hello'  (removes spaces from both sides)
'  '        → ''       (removes all spaces, becomes empty string)
```

---

## Policy 2: Use Only NULL (Best for Storage!)

### The Rule:
- NULL is OK
- Empty strings are NOT OK (convert to NULL)
- Blank spaces are NOT OK (convert to NULL)

### How to Implement:

**Step 1:** Remove blank spaces with TRIM
**Step 2:** Convert empty strings to NULL with NULLIF

```sql
NULLIF(TRIM(Category), '') AS Policy2
```

### How It Works:

**Step-by-Step Process:**

**Original Data:**
```
'A', NULL, '', '  '
```

**After Step 1 (TRIM):**
```
'A', NULL, '', ''  ← Spaces became empty string
```

**After Step 2 (NULLIF):**
```
'A', NULL, NULL, NULL  ← Empty strings became NULL
```

### Example:

| Row | Original | After TRIM | After NULLIF (Final) |
|-----|----------|------------|----------------------|
| 1   | 'A'      | 'A'        | 'A'                  |
| 2   | NULL     | NULL       | NULL                 |
| 3   | ''       | ''         | NULL                 |
| 4   | '  '     | ''         | NULL                 |

### Why This Policy is Great:
- Takes the LEAST storage space
- FASTEST performance
- Clean and simple (only NULL or real values)

---

## Policy 3: Use Default Value "Unknown"

### The Rule:
- NULL is NOT OK (replace with "Unknown")
- Empty strings are NOT OK (replace with "Unknown")
- Blank spaces are NOT OK (replace with "Unknown")

### How to Implement:

**Step 1:** Remove blank spaces with TRIM
**Step 2:** Convert empty strings to NULL with NULLIF  
**Step 3:** Replace NULL with "Unknown" using COALESCE

```sql
COALESCE(NULLIF(TRIM(Category), ''), 'Unknown') AS Policy3
```

### How It Works:

**Step-by-Step Process:**

**Original Data:**
```
'A', NULL, '', '  '
```

**After Step 1 (TRIM):**
```
'A', NULL, '', ''
```

**After Step 2 (NULLIF):**
```
'A', NULL, NULL, NULL
```

**After Step 3 (COALESCE):**
```
'A', 'Unknown', 'Unknown', 'Unknown'
```

### Example:

| Row | Original | Step 1: TRIM | Step 2: NULLIF | Step 3: COALESCE (Final) |
|-----|----------|--------------|----------------|--------------------------|
| 1   | 'A'      | 'A'          | 'A'            | 'A'                      |
| 2   | NULL     | NULL         | NULL           | 'Unknown'                |
| 3   | ''       | ''           | NULL           | 'Unknown'                |
| 4   | '  '     | ''           | NULL           | 'Unknown'                |

### Why This Policy is Great:
- Easy to read in reports
- Users understand "Unknown" better than NULL
- No confusion when viewing results

---

## Visual Comparison of All Three Policies

### Original Messy Data:
```
Category: 'A', NULL, '', '  '
```

### After Policy 1:
```
Result: 'A', NULL, '', ''
(Removed blank spaces only)
```

### After Policy 2:
```
Result: 'A', NULL, NULL, NULL
(Everything is either a value or NULL)
```

### After Policy 3:
```
Result: 'A', 'Unknown', 'Unknown', 'Unknown'
(No NULLs, everything has a value)
```

---

## Complete SQL Code Examples

### Policy 1 - Remove Blank Spaces Only:
```sql
SELECT 
    Category,
    TRIM(Category) AS CleanCategory
FROM Orders
```

### Policy 2 - Convert Everything to NULL:
```sql
SELECT 
    Category,
    NULLIF(TRIM(Category), '') AS CleanCategory
FROM Orders
```

### Policy 3 - Use Default Value:
```sql
SELECT 
    Category,
    COALESCE(NULLIF(TRIM(Category), ''), 'Unknown') AS CleanCategory
FROM Orders
```

---

## When to Use Each Policy

### Policy 1: Allow NULL and Empty String
**When to use:** Almost never!
**Problem:** Confusing - hard to explain to users
**Verdict:** Avoid this one!

### Policy 2: Use Only NULL
**When to use:** 
- When preparing data for storage in database
- During ETL (Extract, Transform, Load) processes
- When you need best performance

**Advantages:**
- Takes least storage space
- Fastest query performance
- Clean and standardized

**Best for:** Backend database work

### Policy 3: Use Default Values
**When to use:**
- Before showing data in reports (Tableau, Power BI, Excel)
- When presenting to end users
- As the final step before display

**Advantages:**
- Easy to read and understand
- Users see "Unknown" instead of blank spaces
- Professional looking reports

**Disadvantages:**
- Takes more storage (storing "Unknown" repeatedly)
- Slower performance

**Best for:** Frontend reports and presentations

---

## The Professional Workflow

### Storage Phase (Database):
Use **Policy 2** (NULL)
```
Raw Data → Clean → Store as NULL
```
**Why?** Saves space, runs fast

### Presentation Phase (Reports):
Use **Policy 3** (Default Value)
```
Pull from Database → Convert NULL to "Unknown" → Show to Users
```
**Why?** Easy to read, professional

---

## Real-World Example: Student Database

### Scenario: Track student email addresses

**Original Messy Data:**
| Student | Email         |
|---------|---------------|
| John    | john@mail.com |
| Sarah   | NULL          |
| Mike    | ''            |
| Anna    | '  '          |

### In Database (Use Policy 2):
| Student | Email         |
|---------|---------------|
| John    | john@mail.com |
| Sarah   | NULL          |
| Mike    | NULL          |
| Anna    | NULL          |

### In Report (Use Policy 3):
| Student | Email         |
|---------|---------------|
| John    | john@mail.com |
| Sarah   | Unknown       |
| Mike    | Unknown       |
| Anna    | Unknown       |

---

## Step-by-Step: Implementing Policies

### Policy 2 Example (Storage):

**Step 1:** Identify the problem
```sql
SELECT Category, DATALENGTH(Category) AS Length
FROM Orders
-- Shows: 'A'(1), NULL(NULL), ''(0), '  '(2)
```

**Step 2:** Apply TRIM
```sql
SELECT TRIM(Category) AS Step1
FROM Orders
-- Shows: 'A', NULL, '', ''
```

**Step 3:** Apply NULLIF
```sql
SELECT NULLIF(TRIM(Category), '') AS Step2
FROM Orders
-- Shows: 'A', NULL, NULL, NULL
```

**Done!** Clean data ready for storage

### Policy 3 Example (Reports):

**All previous steps PLUS:**

**Step 4:** Apply COALESCE
```sql
SELECT COALESCE(NULLIF(TRIM(Category), ''), 'Unknown') AS Final
FROM Orders
-- Shows: 'A', 'Unknown', 'Unknown', 'Unknown'
```

**Done!** Clean data ready for users

---

## Common Mistakes to Avoid

### Mistake 1: Skipping TRIM
```sql
-- WRONG - Doesn't handle blank spaces!
NULLIF(Category, '')
```

**Correct:**
```sql
NULLIF(TRIM(Category), '')
```

### Mistake 2: Using COALESCE Too Early
```sql
-- WRONG - Only fixes NULL, not empty strings or spaces!
COALESCE(Category, 'Unknown')
```

**Correct:**
```sql
COALESCE(NULLIF(TRIM(Category), ''), 'Unknown')
```

### Mistake 3: Using Wrong Policy
**Wrong:** Using Policy 3 for database storage (wastes space!)
**Correct:** Use Policy 2 for storage, Policy 3 for reports

---

## Key Takeaways

**Always clean data BEFORE analysis!**
- Bad data leads to wrong decisions

**Three policies to choose from:**
1. Policy 1: NULL + Empty String (avoid this)
2. Policy 2: Only NULL (best for storage)
3. Policy 3: Default values (best for reports)

**The professional approach:**
- Store data using Policy 2 (NULL)
- Display data using Policy 3 (Default values)

**Three-step cleaning process:**
1. TRIM (remove blank spaces)
2. NULLIF (convert empty to NULL)
3. COALESCE (convert NULL to default value)

**Remember:** Clean data = Accurate analysis = Good decisions!

---

## Practice Exercise

**Original Data:**
```
Products: 'Laptop', NULL, '', '   '
```

**Challenge:** Clean this data using Policy 2

**Steps:**
1. Apply TRIM: 'Laptop', NULL, '', ''
2. Apply NULLIF: 'Laptop', NULL, NULL, NULL

**Answer:**
```sql
SELECT NULLIF(TRIM(Product), '') AS CleanProduct
FROM Products
```

**Result:** Only 'Laptop' and three NULLs - perfectly clean!
