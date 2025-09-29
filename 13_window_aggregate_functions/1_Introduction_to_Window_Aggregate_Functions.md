# SQL Aggregate Functions - Study Notes

## What are Aggregate Functions?

**Aggregate functions** are special tools in SQL that help you **summarize** or **combine** data from many rows into **one single answer**.

### Simple Example:

Imagine you have monthly sales data:
- January: $50
- February: $75
- March: $50

An aggregate function can add all these up to give you: **Total = $175**

**Think of it like:** If you have many test scores and want to find your average - that's what aggregate functions do with data!

---

## How Aggregate Functions Work

When you use an aggregate function:

1. **SQL looks at all the rows** in your data (or a specific group)
2. **It processes all the values** together
3. **It gives you ONE final answer**

**Example:**
- Input: 50, 75, 50 (three numbers)
- Output: 175 (one number - the total!)

---

## Basic Syntax (The Pattern)

All aggregate functions follow a similar pattern:

```
FUNCTION_NAME(expression)
```

### Example: AVERAGE
```
AVG(sales)
```

This means: "Find the average of all sales values"

---

## Parts of Aggregate Functions

### 1. **Function Name**
The action you want to do (like AVG, SUM, COUNT)

### 2. **Expression** (Required)
What data you want to work with (like "sales")
- You CANNOT leave this empty!
- For most functions, this must be a **NUMBER**

**Why numbers?** You can't find the average of names like "John, Sarah, Mike" - that doesn't make sense! But you CAN find the average of ages like 12, 13, 14.

### 3. **PARTITION BY** (Optional)
Divides your data into groups
- You can use it OR skip it
- It's your choice!

### 4. **ORDER BY** (Optional)
Arranges data in order before processing
- You can use it OR skip it
- Not always needed

### 5. **Frame** (Optional)
Defines which specific rows to include
- Can be left empty for aggregate functions

---

## Common Aggregate Functions

Here are the main aggregate functions you'll use:

### 1. **COUNT** 🔢
Counts how many items you have

**Special:** COUNT works with ANY data type (numbers, text, dates)

**Example:** Count how many students are in class

### 2. **SUM** ➕
Adds up all the numbers

**Needs:** Numbers only

**Example:** Add up all your test scores

### 3. **AVG** (Average) ➗
Finds the middle value (average)

**Needs:** Numbers only

**Example:** Find your average test score

### 4. **MIN** ⬇️
Finds the smallest value

**Needs:** Numbers only

**Example:** Find your lowest test score

### 5. **MAX** ⬆️
Finds the largest value

**Needs:** Numbers only

**Example:** Find your highest test score

---

## Quick Reference Table

| Function | What It Does | Data Type Needed | Optional Parts |
|----------|--------------|------------------|----------------|
| COUNT | Counts items | ANY (text, numbers, dates) | PARTITION BY, ORDER BY, Frame |
| SUM | Adds numbers | Numbers only | PARTITION BY, ORDER BY, Frame |
| AVG | Finds average | Numbers only | PARTITION BY, ORDER BY, Frame |
| MIN | Finds smallest | Numbers only | PARTITION BY, ORDER BY, Frame |
| MAX | Finds largest | Numbers only | PARTITION BY, ORDER BY, Frame |

---

## Important Rules to Remember

✅ **COUNT is special** - It's the only function that accepts all types of data (not just numbers)

✅ **All other functions need numbers** - You can't average names or sum text

✅ **PARTITION BY is optional** - Use it to divide data into groups, or skip it

✅ **ORDER BY is optional** - Use it to arrange data, or skip it

✅ **Frame is optional** - Usually empty for aggregate functions

✅ **One answer per function** - Aggregate functions always give you ONE final result

---

## Real-Life Examples

### Example 1: Class Test Scores
- **COUNT**: How many students took the test? → 25 students
- **SUM**: What's the total of all scores? → 2,000 points
- **AVG**: What's the average score? → 80 points
- **MIN**: What's the lowest score? → 45 points
- **MAX**: What's the highest score? → 98 points

### Example 2: Store Sales
- **COUNT**: How many items sold today? → 150 items
- **SUM**: How much money earned? → $3,500
- **AVG**: What's the average price per item? → $23.33
- **MIN**: What's the cheapest item sold? → $5
- **MAX**: What's the most expensive item? → $200

---

## Key Takeaways

🎯 Aggregate functions take MANY rows and give you ONE answer

🎯 COUNT works with any data type; others need numbers

🎯 All the optional parts (PARTITION BY, ORDER BY, Frame) are YOUR choice

🎯 These functions help you understand your data quickly

🎯 They're like using a calculator to summarize lots of information at once

---

## What's Next?

Now we'll learn about each function in detail, starting with **COUNT**! We'll see how they work with real examples and practice writing SQL queries. 🚀
