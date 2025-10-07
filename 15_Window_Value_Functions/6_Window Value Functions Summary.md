# SQL Window Value Functions - Simple Guide

## What Are Value Functions?

Value functions (also called analytical functions) let you look at data from other rows in your table while staying in the same query. Think of it like being able to peek at your friend's answer sheet to compare with yours - but in a good way!

## The Four Main Value Functions

### 1. **LAG** - Look at Previous Rows
Gets the value from a row that came before.

**Example:** Compare this month's sales to last month's sales

```sql
SELECT 
    month_name,
    sales,
    LAG(sales) OVER (ORDER BY month_number) AS previous_month_sales
FROM monthly_sales;
```

**Expected Output:**
```
month_name  | sales | previous_month_sales
------------|-------|--------------------
January     | 1000  | NULL
February    | 1200  | 1000
March       | 1500  | 1200
April       | 1300  | 1500
```

### 2. **LEAD** - Look at Next Rows
Gets the value from a row that comes after.

**Example:** See next month's sales

```sql
SELECT 
    month_name,
    sales,
    LEAD(sales) OVER (ORDER BY month_number) AS next_month_sales
FROM monthly_sales;
```

**Expected Output:**
```
month_name  | sales | next_month_sales
------------|-------|------------------
January     | 1000  | 1200
February    | 1200  | 1500
March       | 1500  | 1300
April       | 1300  | NULL
```

### 3. **FIRST_VALUE** - Get the First Value
Gets the first value in a group.

**Example:** Compare each student's score to the class's highest score

```sql
SELECT 
    student_name,
    score,
    FIRST_VALUE(score) OVER (ORDER BY score DESC) AS highest_score
FROM test_scores;
```

**Expected Output:**
```
student_name | score | highest_score
-------------|-------|---------------
Alice        | 95    | 95
Bob          | 88    | 95
Charlie      | 92    | 95
Diana        | 85    | 95
```

### 4. **LAST_VALUE** - Get the Last Value
Gets the last value in a group.

**Example:** Compare each score to the lowest score

```sql
SELECT 
    student_name,
    score,
    LAST_VALUE(score) OVER (
        ORDER BY score DESC 
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS lowest_score
FROM test_scores;
```

**Expected Output:**
```
student_name | score | lowest_score
-------------|-------|-------------
Alice        | 95    | 85
Bob          | 88    | 85
Charlie      | 92    | 85
Diana        | 85    | 85
```

## Important Rules

1. **ORDER BY is Required** - You must tell SQL how to arrange the rows
2. **Works with Any Data Type** - Numbers, text, dates - all work!
3. **LAST_VALUE Needs Special Frame** - Add the "ROWS BETWEEN..." part for it to work properly

## Real-World Uses

### 📊 Month-over-Month Growth
**Compare this month to last month:**

```sql
SELECT 
    month_name,
    sales,
    sales - LAG(sales) OVER (ORDER BY month_number) AS growth
FROM monthly_sales;
```

**Expected Output:**
```
month_name  | sales | growth
------------|-------|-------
January     | 1000  | NULL
February    | 1200  | 200
March       | 1500  | 300
April       | 1300  | -200
```

### 📅 Time Between Events
**Calculate days between customer orders:**

```sql
SELECT 
    customer_name,
    order_date,
    order_date - LAG(order_date) OVER (
        PARTITION BY customer_name 
        ORDER BY order_date
    ) AS days_since_last_order
FROM orders;
```

**Expected Output:**
```
customer_name | order_date  | days_since_last_order
--------------|-------------|---------------------
John          | 2024-01-05  | NULL
John          | 2024-01-15  | 10
John          | 2024-02-01  | 17
Mary          | 2024-01-10  | NULL
Mary          | 2024-01-20  | 10
```

### 🏆 Compare to Best Performance
**See how far each score is from the top score:**

```sql
SELECT 
    student_name,
    score,
    FIRST_VALUE(score) OVER (ORDER BY score DESC) - score AS points_from_top
FROM test_scores;
```

**Expected Output:**
```
student_name | score | points_from_top
-------------|-------|----------------
Alice        | 95    | 0
Charlie      | 92    | 3
Bob          | 88    | 7
Diana        | 85    | 10
```

## Key Takeaway

Value functions help you answer questions like:
- "How does this compare to last time?"
- "Am I getting better or worse?"
- "How far am I from the best/worst?"

All without needing complicated joins or multiple queries!
