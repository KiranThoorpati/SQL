
1. Find the second highest salary from the Employee table.

```sql
SELECT MAX(salary) AS SecondHighestSalary FROM employees
WHERE salary < ( SELECT MAX(salary) FROM employees);
```
---

I'll explain this SQL query step by step using a simple example that a 7th grader can understand!

## Example Employee Table

Let's say we have 5 employees:

| Employee Name | Salary |
|--------------|---------|
| Alice | 50,000 |
| Bob | 70,000 |
| Charlie | 90,000 |
| David | 60,000 |
| Emma | 90,000 |

## How the Query Works

Think of it like finding the **second tallest student** in your class!

### **Step 1: Find the highest salary (Inner query)**
```sql
SELECT MAX(salary) FROM employees
```

This finds the **maximum (highest) salary** in the table.

**Result:** 90,000 (Charlie and Emma both earn this)

### **Step 2: Find salaries less than the highest**
```sql
WHERE salary < 90,000
```

Now we look at only the employees who earn **less than 90,000**:

| Employee Name | Salary |
|--------------|---------|
| Alice | 50,000 |
| Bob | 70,000 |
| David | 60,000 |

### **Step 3: Find the maximum from remaining salaries**
```sql
SELECT MAX(salary) AS SecondHighestSalary
```

From the remaining salaries (50,000, 70,000, 60,000), find the highest one.

**Result:** 70,000

## Final Answer

**SecondHighestSalary: 70,000** (Bob's salary)

---

## Simple Analogy 🎯

Imagine you're lining up students by height:
1. **First**, find the tallest student (90,000)
2. **Then**, ask that student to sit down
3. **Finally**, find who's the tallest among the remaining students (70,000)

That's your second highest!
