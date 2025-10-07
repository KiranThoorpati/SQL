# SQL Aggregate Functions: The Basics
## A Simple Guide for 7th Grade Students

## What are Aggregate Functions? 🔢

**Aggregate functions** take many rows of data and combine them into ONE single value.

**Think of it like this:**
- You have test scores: 85, 90, 75, 95
- You want to find the **average** → ONE number: 86.25
- Aggregate functions do this automatically!

---

## The 5 Basic Aggregate Functions

| Function | What It Does | Real-Life Example |
|----------|-------------|-------------------|
| **COUNT()** | Counts rows | How many students in class? |
| **SUM()** | Adds up all values | Total points scored in a game |
| **AVG()** | Finds the average | Average temperature this week |
| **MAX()** | Finds the highest value | Highest test score |
| **MIN()** | Finds the lowest value | Lowest price |

---

## Sample Data for Our Examples

Let's say we have an **orders** table:

```
order_id | customer_id | sales
---------|-------------|-------
1        | 101         | 10
2        | 102         | 25
3        | 101         | 35
4        | 103         | 10
```

---

## 1. COUNT() - Counting Rows 📊

**Question:** How many orders do we have?

**Query:**
```sql
SELECT COUNT(*) AS total_number_of_orders
FROM orders;
```

**Output:**
```
total_number_of_orders
-----------------------
4
```

**How it works:** SQL counts each row → 1, 2, 3, 4 = **4 orders**

---

## 2. SUM() - Adding Values ➕

**Question:** What is the total sales amount?

**Query:**
```sql
SELECT SUM(sales) AS total_sales
FROM orders;
```

**Output:**
```
total_sales
------------
80
```

**How it works:** 10 + 25 + 35 + 10 = **80**

---

## 3. AVG() - Finding Average 📈

**Question:** What is the average sales per order?

**Query:**
```sql
SELECT AVG(sales) AS average_sales
FROM orders;
```

**Output:**
```
average_sales
--------------
20
```

**How it works:** (10 + 25 + 35 + 10) ÷ 4 = **20**

---

## 4. MAX() - Finding Highest Value 🏆

**Question:** What is the highest sales amount?

**Query:**
```sql
SELECT MAX(sales) AS highest_sales
FROM orders;
```

**Output:**
```
highest_sales
--------------
35
```

**How it works:** SQL looks at all values and picks the biggest: **35**

---

## 5. MIN() - Finding Lowest Value 📉

**Question:** What is the lowest sales amount?

**Query:**
```sql
SELECT MIN(sales) AS lowest_sales
FROM orders;
```

**Output:**
```
lowest_sales
-------------
10
```

**How it works:** SQL looks at all values and picks the smallest: **10**

---

## Combining Multiple Aggregate Functions 🎯

You can use several functions at once!

**Query:**
```sql
SELECT 
    COUNT(*) AS total_orders,
    SUM(sales) AS total_sales,
    AVG(sales) AS average_sales,
    MAX(sales) AS highest_sales,
    MIN(sales) AS lowest_sales
FROM orders;
```

**Output:**
```
total_orders | total_sales | average_sales | highest_sales | lowest_sales
-------------|-------------|---------------|---------------|-------------
4            | 80          | 20            | 35            | 10
```

**This tells us:**
- We have 4 orders
- Total sales: $80
- Average per order: $20
- Best sale: $35
- Smallest sale: $10

---

## Using GROUP BY to Break Down Data 📊

**GROUP BY** breaks big numbers into smaller groups!

### Example: Sales by Customer

**Query:**
```sql
SELECT 
    customer_id,
    COUNT(*) AS total_orders,
    SUM(sales) AS total_sales,
    AVG(sales) AS average_sales,
    MAX(sales) AS highest_sales,
    MIN(sales) AS lowest_sales
FROM orders
GROUP BY customer_id;
```

**Output:**
```
customer_id | total_orders | total_sales | average_sales | highest_sales | lowest_sales
------------|--------------|-------------|---------------|---------------|-------------
101         | 2            | 45          | 22.5          | 35            | 10
102         | 1            | 25          | 25            | 25            | 25
103         | 1            | 10          | 10            | 10            | 10
```

**How it works:**
- Customer 101: 2 orders (10 + 35 = 45 total)
- Customer 102: 1 order (25 total)
- Customer 103: 1 order (10 total)

---

## Real-World Examples 🌍

### Example 1: Student Test Scores

**Sample Data:**
```
student_id | test_name | score
-----------|-----------|-------
1          | Math      | 85
2          | Math      | 92
3          | Math      | 78
1          | Science   | 90
2          | Science   | 88
3          | Science   | 95
```

**Query:**
```sql
SELECT 
    COUNT(*) AS total_tests,
    AVG(score) AS average_score,
    MAX(score) AS highest_score,
    MIN(score) AS lowest_score
FROM test_scores;
```

**Output:**
```
total_tests | average_score | highest_score | lowest_score
------------|---------------|---------------|-------------
6           | 88            | 95            | 78
```

---

### Example 2: Sales by Student (Group By)

**Query:**
```sql
SELECT 
    student_id,
    COUNT(*) AS tests_taken,
    AVG(score) AS average_score,
    MAX(score) AS best_score,
    MIN(score) AS worst_score
FROM test_scores
GROUP BY student_id;
```

**Output:**
```
student_id | tests_taken | average_score | best_score | worst_score
-----------|-------------|---------------|------------|------------
1          | 2           | 87.5          | 90         | 85
2          | 2           | 90            | 92         | 88
3          | 2           | 86.5          | 95         | 78
```

---

## Example 3: Store Sales Analysis

**Sample Data:**
```
store_id | product  | price
---------|----------|-------
1        | Apple    | 2
1        | Banana   | 1
1        | Orange   | 3
2        | Apple    | 2
2        | Grape    | 4
```

**Query:**
```sql
SELECT 
    store_id,
    COUNT(*) AS products_sold,
    SUM(price) AS total_revenue,
    AVG(price) AS average_price
FROM sales
GROUP BY store_id;
```

**Output:**
```
store_id | products_sold | total_revenue | average_price
---------|---------------|---------------|---------------
1        | 3             | 6             | 2
2        | 2             | 6             | 3
```

---

## Key Points to Remember 💡

1. **Aggregate functions combine many rows into ONE value**
   - Input: Many rows
   - Output: One number

2. **The 5 basic functions:**
   - COUNT() = How many?
   - SUM() = What's the total?
   - AVG() = What's the average?
   - MAX() = What's the highest?
   - MIN() = What's the lowest?

3. **Use GROUP BY to break down data**
   - Without GROUP BY → One big number for everything
   - With GROUP BY → Separate numbers for each group

4. **COUNT(*) vs COUNT(column)**
   - COUNT(*) counts all rows
   - COUNT(column) counts only non-NULL values

---

## Practice Exercise 📝

Try this yourself! Given this data:

```
product  | quantity | price
---------|----------|-------
Apple    | 5        | 10
Banana   | 3        | 6
Orange   | 4        | 12
Apple    | 2        | 10
```

**Challenge Questions:**
1. How many sales happened? (Use COUNT)
2. What's the total quantity sold? (Use SUM)
3. What's the average price? (Use AVG)
4. What's the highest price? (Use MAX)
5. What's the lowest quantity? (Use MIN)

---

## Summary 🎓

Aggregate functions help you understand your data by answering questions like:
- **How many?** → COUNT()
- **How much total?** → SUM()
- **What's typical?** → AVG()
- **What's the best/worst?** → MAX() / MIN()

They turn lots of data into useful insights! 🌟
