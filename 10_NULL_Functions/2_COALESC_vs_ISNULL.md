# SQL Functions to Replace NULL Values - Simple Notes

## 1. ISNULL() Function

### What does it do?
ISNULL replaces NULL values with a specific value you choose.

### Syntax (How to write it):
```
ISNULL(value, replacement_value)
```
- **First part**: The column you want to check
- **Second part**: What to replace NULL with

---

### Two Ways to Use ISNULL:

#### **Way 1: Replace NULL with a fixed value**
```sql
ISNULL(shipping_address, 'Unknown')
```
**Meaning**: If shipping_address is NULL, show "Unknown"

**Example:**
| Order | Shipping Address | Result |
|-------|-----------------|---------|
| 1 | "123 Main St" | "123 Main St" |
| 2 | NULL | "Unknown" |

✅ **Benefit**: You'll NEVER see NULL in your results

---

#### **Way 2: Replace NULL with another column's value**
```sql
ISNULL(shipping_address, billing_address)
```
**Meaning**: If shipping_address is NULL, use billing_address instead

**Example:**
| Order | Shipping Address | Billing Address | Result |
|-------|-----------------|----------------|---------|
| 1 | "123 Main St" | "456 Oak Ave" | "123 Main St" |
| 2 | NULL | "456 Oak Ave" | "456 Oak Ave" |
| 3 | NULL | NULL | NULL |

⚠️ **Problem**: If BOTH columns are NULL, you still get NULL in the result!

---

### How ISNULL Works (Step by Step):

**Question SQL asks**: "Is the first value NULL?"
- **If NO** → Show the original value
- **If YES** → Show the replacement value

---

## 2. COALESCE() Function

### What does it do?
COALESCE returns the **first non-NULL value** from a list of values.

### Syntax:
```
COALESCE(value1, value2, value3, ...)
```
You can add as many values as you want!

---

### Why is COALESCE Better?

#### **With ISNULL** (only 2 values):
```sql
ISNULL(shipping_address, billing_address)
-- Problem: What if both are NULL?
```

#### **With COALESCE** (many values):
```sql
COALESCE(shipping_address, billing_address, 'Unknown')
```
**Meaning**: 
1. First check shipping_address
2. If NULL, check billing_address  
3. If still NULL, use "Unknown"

---

### How COALESCE Works (Step by Step):

**SQL checks from LEFT to RIGHT and STOPS at the first non-NULL value**

**Example:**
```sql
COALESCE(shipping_address, billing_address, 'N/A')
```

| Order | Shipping | Billing | Result | Why? |
|-------|----------|---------|--------|------|
| 1 | "123 Main" | "456 Oak" | "123 Main" | First value is NOT NULL ✓ |
| 2 | NULL | "456 Oak" | "456 Oak" | First is NULL, second is NOT NULL ✓ |
| 3 | NULL | NULL | "N/A" | Both NULL, so use default value ✓ |

✅ **Benefit**: You'll NEVER see NULL in your results (if you add a default value at the end)!

---

## 3. ISNULL vs COALESCE - Which One to Use?

| Feature | ISNULL | COALESCE |
|---------|--------|----------|
| **Number of values** | Only 2 | Unlimited! 🎉 |
| **Speed** | Faster ⚡ | Slightly slower |
| **Works in all databases?** | ❌ No (different names) | ✅ Yes (same everywhere) |
| **Easy to use?** | Simple | Simple |

---

### Different Names in Different Databases:

**ISNULL has different names:**
- Microsoft SQL Server → `ISNULL()`
- Oracle → `NVL()`
- MySQL → `IFNULL()`

**COALESCE is the same everywhere!** ✅

---

## 4. Which Should You Use? 🤔

### **Teacher's Recommendation: Use COALESCE!**

**Why?**
1. ✅ Works the same in ALL databases
2. ✅ Can handle multiple values
3. ✅ If you switch databases later, your code still works!
4. ✅ It's the "standard" way

**When to use ISNULL?**
- Only if you have SERIOUS speed problems
- And you only need to check 2 values

---

## 5. Practice Examples

### Example 1: Student Grades
```sql
COALESCE(test_score, quiz_score, homework_score, 0)
```
**Meaning**: Use test score if available, otherwise quiz score, otherwise homework score, otherwise show 0.

### Example 2: Contact Information
```sql
COALESCE(mobile_phone, home_phone, email, 'No contact info')
```
**Meaning**: Show the first available contact method.

### Example 3: Simple Default
```sql
ISNULL(nickname, 'No nickname')
```
**Meaning**: If student has no nickname, show "No nickname"

---

## Summary in Simple Words:

- **ISNULL**: Checks ONE thing, replaces NULL with something else (2 values max)
- **COALESCE**: Checks MANY things, returns first non-NULL value (unlimited values)
- **Best choice**: Use **COALESCE** because it works everywhere and handles more cases!

**Remember**: Both functions help you avoid seeing NULL in your results by replacing it with useful values! 🎯
