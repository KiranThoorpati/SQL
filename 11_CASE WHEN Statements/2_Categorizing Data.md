# SQL Case Statements: Categorizing Data
## A Simple Guide for 7th Grade Students

## What is a CASE Statement?

A CASE statement in SQL is like making decisions in real life! It's similar to saying:
- "**IF** it's raining, **THEN** I'll take an umbrella"
- "**IF** it's sunny, **THEN** I'll wear sunglasses"
- "**OTHERWISE**, I'll just wear a jacket"

In SQL, we use CASE statements to transform our data and create new information from existing data.

---

## Why Do We Use CASE Statements?

**Main Purpose**: To categorize or group data into different categories based on certain rules.

**Real-Life Example**: Imagine you're a teacher grading test scores:
- Scores above 90 = "Excellent"
- Scores between 70-90 = "Good"
- Scores below 70 = "Needs Improvement"

---

## The Basic Structure

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ELSE default_result
END
```

---

## Let's Learn With an Example!

### The Scenario
Imagine you have a small online store. You want to categorize your sales into three groups:
- **High**: Sales over $50
- **Medium**: Sales between $20 and $50
- **Low**: Sales of $20 or less

### Step 1: Our Original Data

Let's say we have this sales table:

```sql
SELECT order_id, sales
FROM sales_orders;
```

**Output:**
```
order_id | sales
---------|-------
1        | 15
2        | 60
3        | 35
4        | 50
5        | 75
6        | 10
7        | 45
8        | 20
9        | 100
10       | 25
```

---

### Step 2: Adding Categories Using CASE

Now let's add a new column called "category" using a CASE statement:

```sql
SELECT 
    order_id,
    sales,
    CASE
        WHEN sales > 50 THEN 'High'
        WHEN sales > 20 THEN 'Medium'
        ELSE 'Low'
    END AS category
FROM sales_orders;
```

**Output:**
```
order_id | sales | category
---------|-------|----------
1        | 15    | Low
2        | 60    | High
3        | 35    | Medium
4        | 50    | Medium
5        | 75    | High
6        | 10    | Low
7        | 45    | Medium
8        | 20    | Low
9        | 100   | High
10       | 25    | Medium
```

**How it works:**
- Order 1: $15 is not > 50, not > 20, so it goes to ELSE = "Low"
- Order 2: $60 is > 50, so it's "High"
- Order 3: $35 is not > 50, but it IS > 20, so it's "Medium"

---

### Step 3: Finding Total Sales for Each Category

Now let's answer: "How much total money did we make in each category?"

```sql
SELECT 
    category,
    SUM(sales) AS total_sales
FROM (
    SELECT 
        order_id,
        sales,
        CASE
            WHEN sales > 50 THEN 'High'
            WHEN sales > 20 THEN 'Medium'
            ELSE 'Low'
        END AS category
    FROM sales_orders
) AS categorized_data
GROUP BY category
ORDER BY total_sales DESC;
```

**Output:**
```
category | total_sales
---------|-------------
High     | 235
Medium   | 155
Low      | 45
```

**What this tells us:**
- High category sales: $60 + $75 + $100 = $235
- Medium category sales: $35 + $50 + $45 + $25 = $155
- Low category sales: $15 + $10 + $20 = $45

---

## Key Points to Remember

1. **CASE always starts with** `CASE` **and ends with** `END`
2. **WHEN** = the condition you're checking
3. **THEN** = what happens if the condition is true
4. **ELSE** = what happens if none of the conditions are true
5. **Don't forget to give your new column a name using** `AS`

---

## Practice Question

Try this yourself! Categorize student test scores:
- "Excellent" if score > 90
- "Good" if score > 70
- "Needs Work" if score ≤ 70

```sql
SELECT 
    student_name,
    test_score,
    CASE
        WHEN test_score > 90 THEN 'Excellent'
        WHEN test_score > 70 THEN 'Good'
        ELSE 'Needs Work'
    END AS grade_category
FROM student_scores;
```

---

## Summary

CASE statements help us create new information from existing data by putting things into categories. This makes it easier to understand our data and create useful reports!
