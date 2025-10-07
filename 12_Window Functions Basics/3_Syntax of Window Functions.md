# SQL Window Functions: Understanding the Syntax
## A Simple Guide for 7th Grade Students

## The Two Main Parts of Window Functions 🏗️

Every window function has **TWO main parts**:

```sql
FUNCTION(expression) OVER(window_definition)
```

1. **Window Function** - What calculation to do
2. **OVER Clause** - Defines the "window" (group) of data

---

## Breaking Down the Complete Syntax

```sql
FUNCTION(expression) OVER(
    PARTITION BY column
    ORDER BY column
    FRAME clause
)
```

Let's understand each piece!

---

## Part 1: The Window Function

### Three Types of Window Functions

| Type | Functions | What They Do |
|------|-----------|--------------|
| **Aggregate** | COUNT, SUM, AVG, MAX, MIN | Add up, count, find averages |
| **Ranking** | ROW_NUMBER, RANK, DENSE_RANK | Give rankings (1st, 2nd, 3rd...) |
| **Value/Analytic** | LEAD, LAG, FIRST_VALUE, LAST_VALUE | Access specific values |

---

## Part 2: The Expression (Inside the Function)

The **expression** is what you put inside the function - it's the data you want to calculate.

### What Can Go in the Expression?

**It depends on the function!**

#### Example 1: Column Name
```sql
AVG(sales)  -- Calculate average of the sales column
```

#### Example 2: Empty (for ranking functions)
```sql
ROW_NUMBER()  -- No expression needed!
```

#### Example 3: Number
```sql
NTILE(4)  -- Split data into 4 groups
```

#### Example 4: Complex Logic with CASE
```sql
SUM(
    CASE 
        WHEN sales > 50 THEN 1 
        ELSE 0 
    END
)  -- Count how many sales are over 50
```

---

## Which Data Types Are Allowed?

### Aggregate Functions

| Function | Allowed Data Types |
|----------|-------------------|
| COUNT() | ✅ Any data type |
| SUM() | ✅ Numbers only |
| AVG() | ✅ Numbers only |
| MAX() | ✅ Numbers only |
| MIN() | ✅ Numbers only |

### Ranking Functions

| Function | Expression |
|----------|------------|
| ROW_NUMBER() | ❌ Empty (nothing) |
| RANK() | ❌ Empty (nothing) |
| DENSE_RANK() | ❌ Empty (nothing) |
| NTILE() | ✅ Numbers only |

### Value/Analytic Functions

| Function | Allowed Data Types |
|----------|-------------------|
| LEAD() | ✅ Any data type |
| LAG() | ✅ Any data type |
| FIRST_VALUE() | ✅ Any data type |
| LAST_VALUE() | ✅ Any data type |

---

## Part 3: The OVER Clause 🪟

The **OVER** keyword tells SQL: "This is a window function!"

```sql
FUNCTION() OVER()
```

**Inside OVER**, you can add three optional parts:
1. **PARTITION BY** - Split data into groups
2. **ORDER BY** - Sort data within groups
3. **FRAME** - Define which rows to include (advanced)

**Important:** Everything inside OVER is **optional**! You can leave it empty!

---

## Examples: Understanding Each Part

### Example 1: Basic Window Function (Empty OVER)

**Sample Data:**
```
order_id | product | sales
---------|---------|-------
1        | Apple   | 50
2        | Banana  | 30
3        | Apple   | 70
4        | Banana  | 40
```

**Query:**
```sql
SELECT 
    order_id,
    product,
    sales,
    SUM(sales) OVER() AS total_all_sales
FROM orders;
```

**Output:**
```
order_id | product | sales | total_all_sales
---------|---------|-------|----------------
1        | Apple   | 50    | 190
2        | Banana  | 30    | 190
3        | Apple   | 70    | 190
4        | Banana  | 40    | 190
```

**Explanation:** `OVER()` is empty, so it calculates over ALL rows (50+30+70+40 = 190)

---

### Example 2: COUNT Function

**Query:**
```sql
SELECT 
    order_id,
    product,
    sales,
    COUNT(*) OVER() AS total_orders
FROM orders;
```

**Output:**
```
order_id | product | sales | total_orders
---------|---------|-------|-------------
1        | Apple   | 50    | 4
2        | Banana  | 30    | 4
3        | Apple   | 70    | 4
4        | Banana  | 40    | 4
```

**Explanation:** Counts all 4 orders for each row

---

### Example 3: AVG Function

**Query:**
```sql
SELECT 
    order_id,
    product,
    sales,
    AVG(sales) OVER() AS average_sales
FROM orders;
```

**Output:**
```
order_id | product | sales | average_sales
---------|---------|-------|---------------
1        | Apple   | 50    | 47.5
2        | Banana  | 30    | 47.5
3        | Apple   | 70    | 47.5
4        | Banana  | 40    | 47.5
```

**Explanation:** Average = (50+30+70+40) ÷ 4 = 47.5

---

### Example 4: MAX and MIN Functions

**Query:**
```sql
SELECT 
    order_id,
    product,
    sales,
    MAX(sales) OVER() AS highest_sale,
    MIN(sales) OVER() AS lowest_sale
FROM orders;
```

**Output:**
```
order_id | product | sales | highest_sale | lowest_sale
---------|---------|-------|--------------|------------
1        | Apple   | 50    | 70           | 30
2        | Banana  | 30    | 70           | 30
3        | Apple   | 70    | 70           | 30
4        | Banana  | 40    | 70           | 30
```

**Explanation:** 
- Highest sale across all orders: 70
- Lowest sale across all orders: 30

---

### Example 5: Multiple Aggregate Functions Together

**Query:**
```sql
SELECT 
    order_id,
    product,
    sales,
    COUNT(*) OVER() AS total_orders,
    SUM(sales) OVER() AS total_sales,
    AVG(sales) OVER() AS avg_sales,
    MAX(sales) OVER() AS max_sales,
    MIN(sales) OVER() AS min_sales
FROM orders;
```

**Output:**
```
order_id | product | sales | total_orders | total_sales | avg_sales | max_sales | min_sales
---------|---------|-------|--------------|-------------|-----------|-----------|----------
1        | Apple   | 50    | 4            | 190         | 47.5      | 70        | 30
2        | Banana  | 30    | 4            | 190         | 47.5      | 70        | 30
3        | Apple   | 70    | 4            | 190         | 47.5      | 70        | 30
4        | Banana  | 40    | 4            | 190         | 47.5      | 70        | 30
```

**Cool!** You can use multiple window functions in one query!

---

### Example 6: Using CASE Inside Window Function

**Query:**
```sql
SELECT 
    order_id,
    product,
    sales,
    SUM(
        CASE 
            WHEN sales > 50 THEN 1 
            ELSE 0 
        END
    ) OVER() AS high_value_orders
FROM orders;
```

**Output:**
```
order_id | product | sales | high_value_orders
---------|---------|-------|-------------------
1        | Apple   | 50    | 1
2        | Banana  | 30    | 1
3        | Apple   | 70    | 1
4        | Banana  | 40    | 1
```

**Explanation:** Only order 3 (70) is greater than 50, so count = 1

---

## Visual Guide: Parts of Window Function Syntax

```sql
    AVG(sales)         OVER()
    ↓                  ↓
    Function           Window Definition
    
    
    AVG(sales)         OVER(PARTITION BY product)
    ↓       ↓          ↓    ↓
    Function Expression Window Definition
```

---

## Key Rules to Remember 📝

1. **OVER() is required** - It tells SQL this is a window function
2. **OVER() can be empty** - Calculates across all rows
3. **Expression rules vary** - Different functions accept different data types
4. **Ranking functions need empty expressions** - ROW_NUMBER(), RANK(), etc.
5. **Aggregate functions need columns or numbers** - SUM(sales), AVG(score)

---

## Common Mistakes to Avoid ❌

### Mistake 1: Wrong Data Type
```sql
-- ❌ WRONG: AVG only accepts numbers
AVG(product_name) OVER()

-- ✅ CORRECT
AVG(sales) OVER()
```

### Mistake 2: Forgetting OVER
```sql
-- ❌ WRONG: Missing OVER clause
SUM(sales)

-- ✅ CORRECT
SUM(sales) OVER()
```

### Mistake 3: Adding Expression to Ranking Functions
```sql
-- ❌ WRONG: ROW_NUMBER doesn't take expressions
ROW_NUMBER(sales) OVER()

-- ✅ CORRECT
ROW_NUMBER() OVER()
```

---

## Summary 🎓

### Window Function Structure:
```
FUNCTION(expression) OVER(window_definition)
```

### Three Function Types:
1. **Aggregate** - SUM, AVG, COUNT, MAX, MIN
2. **Ranking** - ROW_NUMBER, RANK, DENSE_RANK
3. **Value** - LEAD, LAG, FIRST_VALUE, LAST_VALUE

### The OVER Clause:
- **Required** for all window functions
- Can be **empty** or contain PARTITION BY, ORDER BY, FRAME
- Defines which rows to calculate over

**Remember:** Window functions are like regular functions with superpowers - they keep all your data while doing calculations! 🦸‍♂️✨
