# SQL Notes: The NULLIF Function

## What is NULLIF?

NULLIF is a special function that **compares two values** and does something interesting:
- If they are **EQUAL** → Returns NULL
- If they are **NOT EQUAL** → Returns the first value

### Think of it like this:
It's the OPPOSITE of COALESCE!
- **COALESCE:** Replaces NULL with a real value
- **NULLIF:** Replaces a real value with NULL

---

## Syntax (How to Write It)

```sql
NULLIF(value1, value2)
```

**Rules:**
✅ Takes exactly TWO values (not more, not less)
✅ Can compare a column with a number
✅ Can compare two columns with each other
❌ You NEVER get value2 in the result - it's only used for checking!

---

## How NULLIF Works - The Flow

```
Step 1: Compare value1 and value2
        ↓
Step 2: Are they equal?
        ↙          ↘
      YES          NO
       ↓            ↓
   Return NULL   Return value1
```

**Possible Results:**
- Result 1: NULL (if values are equal)
- Result 2: The first value (if values are NOT equal)

---

## Example 1: Fixing Bad Data (Negative Prices)

### The Problem:
Some products have price = -1, which means "unknown price" but it's written wrong!

### The Solution:
```sql
NULLIF(Price, -1)
```

### What Happens:

**Original Data:**
| Order | Price |
|-------|-------|
| 1     | 90    |
| 2     | -1    |

**After NULLIF(Price, -1):**
| Order | Result |
|-------|--------|
| 1     | 90     | ← Not equal to -1, so return 90
| 2     | NULL   | ← Equal to -1, so return NULL

### Step-by-Step for Order 1:
1. Is 90 equal to -1? **NO**
2. Return the first value → **90**

### Step-by-Step for Order 2:
1. Is -1 equal to -1? **YES**
2. Return NULL → **NULL**

**Result:** We cleaned up bad data by replacing -1 with NULL!

---

## Example 2: Finding Mistakes in Data

### The Situation:
A store has original prices and discount prices. But sometimes they're the SAME - that's a mistake!

### The Code:
```sql
NULLIF(OriginalPrice, DiscountPrice)
```

### What Happens:

**Original Data:**
| Order | Original Price | Discount Price |
|-------|----------------|----------------|
| 1     | 150            | 120            |
| 2     | 250            | 250            |

**After NULLIF(OriginalPrice, DiscountPrice):**
| Order | Result |
|-------|--------|
| 1     | 150    | ← Prices are different, return 150
| 2     | NULL   | ← Prices are same - ERROR! Return NULL

### Why This is Useful:
NULL acts like a **red flag** 🚩 saying "Hey! Look here! Something's wrong!"

If Original Price = Discount Price, there's no discount at all - that's a data entry mistake!

---

## Example 3: Preventing "Divide by Zero" Error 🛑

### The Problem:
You can't divide any number by zero - your computer will show an ERROR!

### Task:
Find the price per item by dividing Sales by Quantity.

```sql
SELECT 
    OrderID,
    Sales,
    Quantity,
    Sales / Quantity AS Price
FROM Sales.Orders
```

**ERROR:** "Divide by zero error encountered!"

### Why the Error?
| OrderID | Sales | Quantity | Calculation |
|---------|-------|----------|-------------|
| 1       | 100   | 5        | 100/5 = 20 ✓|
| 10      | 50    | 0        | 50/0 = ❌ ERROR!|

---

### The Solution: Use NULLIF!

```sql
SELECT 
    OrderID,
    Sales,
    Quantity,
    Sales / NULLIF(Quantity, 0) AS Price
FROM Sales.Orders
```

### How It Works:

**Before NULLIF:**
| OrderID | Sales | Quantity |
|---------|-------|----------|
| 1       | 100   | 5        |
| 10      | 50    | 0        |

**After NULLIF(Quantity, 0):**
| OrderID | Sales | Quantity → After NULLIF |
|---------|-------|-------------------------|
| 1       | 100   | 5 (stays 5)             |
| 10      | 50    | NULL (was 0)            |

**Final Calculation:**
| OrderID | Calculation | Price |
|---------|-------------|-------|
| 1       | 100 / 5     | 20    |
| 10      | 50 / NULL   | NULL  |

**Important Rule:** Anything divided by NULL = NULL (not an error!)

---

## Why Use NULLIF for Division?

### Without NULLIF:
```
50 ÷ 0 = 💥 ERROR! Program stops!
```

### With NULLIF:
```
50 ÷ NULL = NULL (No error, program continues!)
```

**Getting NULL is MUCH BETTER than getting an error!** ✓

---

## Real-World Example - Pizza Shop 🍕

Imagine you run a pizza shop and track orders:

| Order | Total Sales | Number of Pizzas |
|-------|-------------|------------------|
| 1     | $60         | 3                |
| 2     | $80         | 4                |
| 3     | $40         | 0                |

**Goal:** Find price per pizza

**Without NULLIF:**
- Order 1: $60 ÷ 3 = $20 per pizza ✓
- Order 2: $80 ÷ 4 = $20 per pizza ✓
- Order 3: $40 ÷ 0 = **ERROR!** 💥

**With NULLIF:**
- Order 1: $60 ÷ 3 = $20 per pizza ✓
- Order 2: $80 ÷ 4 = $20 per pizza ✓
- Order 3: $40 ÷ NULL = NULL (means "can't calculate") ✓

Order 3 probably has a data entry mistake - zero pizzas shouldn't have $40 in sales!

---

## Quick Summary Table

| Scenario | What NULLIF Does |
|----------|------------------|
| Bad data (like -1 for price) | Replace with NULL to mark it as "unknown" |
| Two columns are equal when they shouldn't be | Return NULL as a warning flag |
| Dividing by zero | Replace zero with NULL to avoid errors |

---

## Key Takeaways

🎯 **NULLIF compares two values:**
- Equal? → Return NULL
- Not equal? → Return first value

🎯 **The second value is ONLY for checking** - you never see it in results

🎯 **Common uses:**
1. Clean up bad data
2. Flag mistakes in data
3. Prevent "divide by zero" errors

🎯 **Remember:** NULLIF is the OPPOSITE of COALESCE
- COALESCE: NULL → Real Value
- NULLIF: Real Value → NULL

---

## Memory Trick 🧠

**"NULL-IF they match!"**

If the two values match (are equal), you get NULL!

---

## Practice Questions

**Question 1:** What will `NULLIF(50, 50)` return?
**Answer:** NULL (because 50 equals 50)

**Question 2:** What will `NULLIF(50, 30)` return?
**Answer:** 50 (because 50 does not equal 30)

**Question 3:** Why use `NULLIF(Quantity, 0)` when dividing?
**Answer:** To prevent "divide by zero" errors! It replaces 0 with NULL, and dividing by NULL gives NULL (not an error).
