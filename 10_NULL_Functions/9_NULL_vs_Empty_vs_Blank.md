# SQL Notes: NULL vs Empty String vs Blank Spaces

## The Three Confusing Cases

Many people get confused between these three things in databases. They LOOK similar but are completely DIFFERENT!

### The Three Types:

1. **NULL** = "I don't know what the value is" 🤷
2. **Empty String** = "I know the value - it's nothing!" 📭
3. **Blank Spaces** = "There are invisible space characters here!" 👻

---

## Visual Representation

### How They Look in SQL:

```sql
-- Example Data
Value 1: 'A'          -- Normal value
Value 2: NULL         -- NULL (unknown)
Value 3: ''           -- Empty string (two quotes, nothing between)
Value 4: ' '          -- Blank space (two quotes, space between)
```

### When You See Results:
| Row | Category | What You See |
|-----|----------|--------------|
| 1   | A        | A            |
| 2   | NULL     | NULL         |
| 3   | ''       | (looks empty)|
| 4   | ' '      | (looks empty)|

**The Problem:** Rows 3 and 4 look EXACTLY THE SAME! 😱

---

## The Detective Trick: Using LENGTH Function 🔍

To find out what's really there, use the LENGTH (or DATALENGTH) function!

### SQL Code:
```sql
SELECT 
    Category,
    DATALENGTH(Category) AS CategoryLength
FROM Orders
```

### Results:
| Row | Category | CategoryLength | What It Really Is |
|-----|----------|----------------|-------------------|
| 1   | A        | 1              | Normal character  |
| 2   | NULL     | NULL           | NULL (unknown)    |
| 3   | ''       | 0              | Empty string!     |
| 4   | ' '      | 1              | One hidden space! |

**Now we can tell the difference!** ✓

---

## Understanding Each Type

### 1. NULL - The Unknown Value

**Meaning:** "I have no idea what this value is!"

**Think of it like:** An empty box with a question mark on it ❓

**Example:** 
- A student's test score before they take the test = NULL
- You don't know the score yet because it hasn't happened!

**Representation in Table:**
```sql
Category: NULL
```

---

### 2. Empty String - Known to be Nothing

**Meaning:** "I know what this is - it's deliberately empty!"

**Think of it like:** An empty box that you checked and confirmed has nothing inside 📦✓

**Example:**
- A student's middle name when they don't have one = '' (empty string)
- You KNOW they don't have a middle name (not that you don't know it)

**Representation in Table:**
```sql
Category: ''
Length: 0 characters
```

---

### 3. Blank Spaces - Invisible Characters

**Meaning:** "There are actual space characters here (but you can't see them!)"

**Think of it like:** A box with invisible ghost particles inside 👻

**Example:**
- Someone fills out a form and accidentally hits the spacebar
- Now the database has one or more space characters stored!

**Representation in Table:**
```sql
Category: ' '    -- One space
Category: '  '   -- Two spaces
Category: '   '  -- Three spaces
Length: 1, 2, or 3 characters
```

---

## The Evil Problem with Blank Spaces! 😈

### Why Blank Spaces Are "Evil":

1. **They're Hidden:** You can't see them!
2. **They Take Up Space:** Each space uses storage in the database
3. **They're Mistakes:** Usually happen by accident (hitting spacebar)
4. **They Cause Confusion:** They look empty but aren't!

### Example Problem:

**Scenario:** You search for empty categories

```sql
WHERE Category = ''
```

**Result:** You WON'T find rows with blank spaces! They're technically not empty!

---

## Complete Comparison Table

| Feature | NULL | Empty String | Blank Spaces |
|---------|------|--------------|--------------|
| **Representation** | `NULL` | `''` | `' '` or `'  '` |
| **Meaning** | Unknown | Known, but nothing | Known, space characters |
| **Data Type** | No type (special marker) | String | String |
| **Size** | N/A | 0 characters | 1 or more characters |
| **Storage Used** | Very little ✓✓✓ | Some | More (wasteful!) |
| **Speed/Performance** | Fastest ✓✓✓ | Fast ✓✓ | Slowest ✓ |
| **How to Search** | `IS NULL` | `= ''` | `= ' '` |
| **When to Use** | Value is unknown | Value is known to be empty | (Usually a mistake!) |

---

## Real-World Examples

### Example 1: Student Database 🎓

| Student | MiddleName | What It Means |
|---------|------------|---------------|
| John    | NULL       | We don't know if he has a middle name |
| Sarah   | ''         | She confirmed she has NO middle name |
| Mike    | ' '        | Error! Someone hit spacebar by mistake |

---

### Example 2: Online Shopping 🛒

| Order | DeliveryNotes | What It Means |
|-------|---------------|---------------|
| 101   | NULL          | Customer hasn't filled out delivery notes yet |
| 102   | ''            | Customer intentionally left notes blank |
| 103   | ' '           | Mistake! Accidental spacebar press |

---

### Example 3: Contact Information 📱

| Person | PhoneNumber | What It Means |
|--------|-------------|---------------|
| Anna   | NULL        | We don't have her phone number |
| Bob    | ''          | He chose not to provide a phone number |
| Carol  | '  '        | Data entry error (spaces entered) |

---

## How to Detect and Fix Blank Spaces

### Step 1: Find Blank Spaces
```sql
SELECT 
    Category,
    DATALENGTH(Category) AS Length
FROM Orders
WHERE Category = ' '    -- One space
   OR Category = '  '   -- Two spaces
   OR DATALENGTH(Category) > 0 AND Category LIKE ' %'
```

### Step 2: Count Hidden Spaces
```sql
SELECT 
    Category,
    DATALENGTH(Category) AS NumberOfSpaces
FROM Orders
WHERE Category <> ''    -- Not empty string
  AND Category IS NOT NULL  -- Not NULL
```

If Length = 2, there are 2 hidden spaces!

### Step 3: Clean the Data
You can replace blank spaces with NULL or empty string (we'll learn this later!)

---

## How to Search for Each Type

### Searching for NULL:
```sql
-- Find NULL values
WHERE Category IS NULL

-- Find NOT NULL values
WHERE Category IS NOT NULL
```

### Searching for Empty String:
```sql
-- Find empty strings
WHERE Category = ''
```

### Searching for Blank Spaces:
```sql
-- Find one blank space
WHERE Category = ' '

-- Find two blank spaces
WHERE Category = '  '

-- Find any amount of blank spaces
WHERE DATALENGTH(Category) > 0 
  AND LTRIM(RTRIM(Category)) = ''
```

---

## Storage and Performance Comparison

### Storage Space Used (from best to worst):
1. 🥇 **NULL** - Uses almost no space! Winner!
2. 🥈 **Empty String** - Uses a tiny bit of space
3. 🥉 **Blank Spaces** - Wastes space (the more spaces, the more waste!)

### Speed/Performance (from fastest to slowest):
1. 🚀 **NULL** - Fastest! Super speed!
2. ✈️ **Empty String** - Fast (but not as fast as NULL)
3. 🐌 **Blank Spaces** - Slowest! Bad for performance!

**Best Practice:** If you need to show "no value," use NULL instead of blank spaces!

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Using = with NULL
```sql
WHERE Category = NULL  -- WRONG! Won't work!
```
**✓ Correct:**
```sql
WHERE Category IS NULL
```

### ❌ Mistake 2: Not Checking for Blank Spaces
```sql
WHERE Category = ''  -- Only finds empty strings, misses blank spaces!
```
**✓ Better:**
```sql
WHERE (Category = '' OR LTRIM(RTRIM(Category)) = '')
```

### ❌ Mistake 3: Trusting Your Eyes
"It looks empty, so it must be empty string!"
**✓ Always check with DATALENGTH function!**

---

## Key Takeaways

🎯 **Three different types:**
- NULL = Don't know the value (unknown)
- Empty String = Know it's nothing (0 characters)
- Blank Spaces = Has invisible space characters (evil!)

🎯 **They look similar but are completely different!**

🎯 **Use DATALENGTH function to detect the difference**
- NULL → Returns NULL
- Empty String → Returns 0
- Blank Spaces → Returns 1, 2, 3, etc.

🎯 **Best choice for "no value" = NULL**
- Takes least space
- Runs fastest
- Industry standard

🎯 **Blank spaces are usually mistakes!**
- Happen when someone hits spacebar accidentally
- Should be cleaned up from databases

---

## Memory Tricks 🧠

**"NULL = Question Mark, Empty = Zero, Spaces = Ghost Numbers"**
- NULL = ❓ (don't know)
- Empty String = 0️⃣ (zero characters)
- Blank Spaces = 👻1️⃣, 👻2️⃣, 👻3️⃣ (invisible characters)

**"Length Zero = Empty, Length Something = Spaces!"**

---

## Practice Questions

**Question 1:** What's the length of an empty string?
**Answer:** 0 (zero characters)

**Question 2:** What's the length of `'  '` (two spaces)?
**Answer:** 2 (two space characters)

**Question 3:** How do you search for NULL values?
**Answer:** Use `WHERE ColumnName IS NULL` (never use = NULL)

**Question 4:** Which uses the most storage: NULL, empty string, or blank spaces?
**Answer:** Blank spaces use the most storage (and they slow down the database!)

**Question 5:** Why are blank spaces called "evil"?
**Answer:** Because they're invisible, waste space, slow down the database, and are usually mistakes!
