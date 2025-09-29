# SQL Operators - Complete Guide for Students

## What are SQL Operators?

SQL operators are special symbols or words that help us compare, combine, and filter data in databases. Think of them as tools that help us ask specific questions about our data.

## 5 Types of SQL Operators

### 1. **Comparison Operators** 🔍
Used to compare two values

### 2. **Logical Operators** 🧠
Used to combine multiple conditions

### 3. **Range Operators** 📊
Used to check if a value falls within a range

### 4. **Membership Operators** 📝
Used to check if a value exists in a list

### 5. **Search Operators** 🔎
Used to search for patterns in text

---

## 1. Comparison Operators

These operators compare two things and give us TRUE or FALSE answers.

**Basic Formula:** `Expression1 OPERATOR Expression2`

### What Can We Compare?
- Column with Column: `first_name = last_name`
- Column with Value: `country = 'USA'`
- Function Result with Value: `UPPER(name) = 'JOHN'`
- Expression with Value: `price * quantity = 1000`

### Sample Data for Examples:
| Name   | Country | Score |
|--------|---------|-------|
| Maria  | Germany | 350   |
| John   | USA     | 900   |
| George | UK      | 750   |
| Martin | Germany | 500   |
| Peter  | USA     | 200   |

### 6 Comparison Operators:

#### **= (Equal)**
Checks if two values are exactly the same.

**Example:** Find all customers from Germany
```sql
SELECT * FROM customers
WHERE country = 'Germany';
```
**Result:** Maria and Martin (both from Germany)

#### **!= or <> (Not Equal)**
Checks if two values are different.

**Example:** Find customers NOT from Germany
```sql
SELECT * FROM customers
WHERE country != 'Germany';
```
**Result:** John, George, Peter (not from Germany)

#### **> (Greater Than)**
Checks if first value is bigger than second value.

**Example:** Find customers with score higher than 500
```sql
SELECT * FROM customers
WHERE score > 500;
```
**Result:** John (900) and George (750)

#### **>= (Greater Than or Equal)**
Checks if first value is bigger than OR equal to second value.

**Example:** Find customers with score 500 or more
```sql
SELECT * FROM customers
WHERE score >= 500;
```
**Result:** John (900), George (750), and Martin (500)

#### **< (Less Than)**
Checks if first value is smaller than second value.

**Example:** Find customers with score less than 500
```sql
SELECT * FROM customers
WHERE score < 500;
```
**Result:** Maria (350) and Peter (200)

#### **<= (Less Than or Equal)**
Checks if first value is smaller than OR equal to second value.

**Example:** Find customers with score 500 or less
```sql
SELECT * FROM customers
WHERE score <= 500;
```
**Result:** Maria (350), Martin (500), and Peter (200)

---

## How SQL Evaluates Conditions

When SQL checks a condition, it goes through each row one by one:

**Example:** `WHERE country = 'USA'`

**Row 1 - Maria (Germany):**
- Is Germany = USA? ❌ FALSE → Remove from results

**Row 2 - John (USA):**
- Is USA = USA? ✅ TRUE → Keep in results

**Row 3 - George (UK):**
- Is UK = USA? ❌ FALSE → Remove from results

**Row 4 - Martin (Germany):**
- Is Germany = USA? ❌ FALSE → Remove from results

**Row 5 - Peter (USA):**
- Is USA = USA? ✅ TRUE → Keep in results

**Final Result:** Only John and Peter remain

---

## Quick Reference Table

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| = | Equal to | `age = 15` | Students exactly 15 years old |
| != or <> | Not equal to | `grade != 'F'` | All grades except F |
| > | Greater than | `score > 80` | Scores above 80 |
| >= | Greater than or equal | `score >= 80` | Scores 80 and above |
| < | Less than | `price < 100` | Items under $100 |
| <= | Less than or equal | `price <= 100` | Items $100 and under |

---

## Practice Questions

1. Write a query to find all customers with score exactly 750.
2. Write a query to find all customers NOT from UK.
3. Write a query to find all customers with score 400 or higher.

### Answers:
1. `SELECT * FROM customers WHERE score = 750;`
2. `SELECT * FROM customers WHERE country != 'UK';`
3. `SELECT * FROM customers WHERE score >= 400;`

---

## Key Points to Remember

✅ **Always use single quotes** for text values: `'USA'`, `'Germany'`

✅ **No quotes needed** for numbers: `500`, `750`

✅ **Be exact** with spelling and capitalization

✅ **SQL goes row by row** checking each condition

✅ **Only TRUE conditions** keep rows in results

---

*This covers the Comparison Operators section. Logical Operators (AND, OR, NOT) will be covered in the next lesson!*
