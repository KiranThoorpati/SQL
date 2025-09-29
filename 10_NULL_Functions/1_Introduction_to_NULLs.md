# SQL NULL Values - Simple Notes

## What is NULL?

**NULL** means "nothing" or "no value" in a database.

### Real-Life Example:
Imagine filling out a form to create an account:
- **Required fields**: Name, Email (you MUST fill these)
- **Optional fields**: Phone number, Address (you CAN leave empty)

When you skip optional fields and click "Register," those empty fields become **NULL** in the database.

### Important Points about NULL:
- NULL = "I don't know" or "missing information"
- NULL is NOT the same as:
  - Zero (0)
  - Empty text ("")
  - Blank space (" ")
- NULL simply means **no value exists**

---

## SQL Functions to Handle NULL Values

### 1. **Checking for NULL Values**

#### **IS NULL**
- Checks if a value is NULL
- Returns: TRUE or FALSE

**Example**: Find students who didn't provide phone numbers
```
WHERE phone_number IS NULL
```
Result: TRUE (if phone number is missing)

#### **IS NOT NULL**
- Checks if a value is NOT NULL (has some value)
- Returns: TRUE or FALSE

**Example**: Find students who provided phone numbers
```
WHERE phone_number IS NOT NULL
```
Result: FALSE (if phone number is missing)

---

### 2. **Replacing NULL with a Value**

#### **ISNULL()**
- Replaces NULL with a value you choose
- Does NOT change the original data

**Example**: If age is NULL, show 18 instead
```
ISNULL(age, 18)
```

#### **COALESCE()**
- Similar to ISNULL but more powerful
- Can check multiple values and return the first non-NULL value

**Example**: Use the first available contact method
```
COALESCE(mobile, home_phone, email)
```

---

### 3. **Replacing a Value with NULL**

#### **NULLIF()**
- Changes a specific value to NULL
- Opposite of ISNULL

**Example**: If score is 0, make it NULL
```
NULLIF(score, 0)
```

---

## Summary Table

| Function | Purpose | Example |
|----------|---------|---------|
| **IS NULL** | Check if value is missing | `WHERE age IS NULL` |
| **IS NOT NULL** | Check if value exists | `WHERE age IS NOT NULL` |
| **ISNULL()** | Replace NULL with a value | `ISNULL(age, 18)` |
| **COALESCE()** | Replace NULL with first available value | `COALESCE(phone1, phone2)` |
| **NULLIF()** | Replace a value with NULL | `NULLIF(score, 0)` |

---

## Key Takeaway:
These functions help us **handle missing information** in databases - either by checking for it, replacing it with something useful, or converting values to NULL when needed.
