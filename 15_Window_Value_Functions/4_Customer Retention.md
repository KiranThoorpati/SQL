# SQL Use Case: Customer Retention Analysis
### Finding Your Most Loyal Customers

## What is Customer Retention Analysis?

**Customer Retention** means keeping your customers happy so they keep coming back! It's like having friends who regularly visit your lemonade stand instead of just coming once.

### Why is it Important?
- 🎯 **Identify Loyal Customers**: Find who buys from you regularly
- 💡 **Understand Behavior**: See how often customers return
- 🏆 **Reward Loyalty**: Give special attention to best customers

---

## The Problem We're Solving

**Task**: Rank customers based on the average number of days between their orders. The shorter the gap, the more loyal the customer!

---

## Step-by-Step Solution

### Step 1: Create Sample Data

```sql
CREATE TABLE sales_orders (
    order_id INT,
    customer_id INT,
    order_date DATE
);

INSERT INTO sales_orders VALUES
(101, 1, '2024-01-10'),
(102, 1, '2024-01-20'),
(103, 1, '2024-02-15'),
(104, 2, '2024-01-05'),
(105, 2, '2024-02-10'),
(106, 3, '2024-01-15'),
(107, 3, '2024-02-20'),
(108, 4, '2024-03-01');
```

**Our Data:**
| order_id | customer_id | order_date |
|----------|-------------|------------|
| 101      | 1           | 2024-01-10 |
| 102      | 1           | 2024-01-20 |
| 103      | 1           | 2024-02-15 |
| 104      | 2           | 2024-01-05 |
| 105      | 2           | 2024-02-10 |
| 106      | 3           | 2024-01-15 |
| 107      | 3           | 2024-02-20 |
| 108      | 4           | 2024-03-01 |

---

### Step 2: Get Current Order and Next Order Dates

We use **LEAD** to see when each customer ordered next:

```sql
SELECT 
    order_id,
    customer_id,
    order_date AS current_order,
    LEAD(order_date) OVER (
        PARTITION BY customer_id 
        ORDER BY order_date
    ) AS next_order
FROM sales_orders
ORDER BY customer_id, order_date;
```

**Output:**
| order_id | customer_id | current_order | next_order |
|----------|-------------|---------------|------------|
| 101      | 1           | 2024-01-10    | 2024-01-20 |
| 102      | 1           | 2024-01-20    | 2024-02-15 |
| 103      | 1           | 2024-02-15    | NULL       |
| 104      | 2           | 2024-01-05    | 2024-02-10 |
| 105      | 2           | 2024-02-10    | NULL       |
| 106      | 3           | 2024-01-15    | 2024-02-20 |
| 107      | 3           | 2024-02-20    | NULL       |
| 108      | 4           | 2024-03-01    | NULL       |

**What happened?**
- **PARTITION BY customer_id**: Looks at each customer separately
- Last order for each customer = NULL (no next order yet)

---

### Step 3: Calculate Days Between Orders

```sql
SELECT 
    order_id,
    customer_id,
    order_date AS current_order,
    LEAD(order_date) OVER (
        PARTITION BY customer_id 
        ORDER BY order_date
    ) AS next_order,
    DATEDIFF(
        DAY,
        order_date,
        LEAD(order_date) OVER (
            PARTITION BY customer_id 
            ORDER BY order_date
        )
    ) AS days_until_next_order
FROM sales_orders
ORDER BY customer_id, order_date;
```

**Output:**
| order_id | customer_id | current_order | next_order | days_until_next_order |
|----------|-------------|---------------|------------|-----------------------|
| 101      | 1           | 2024-01-10    | 2024-01-20 | 10                    |
| 102      | 1           | 2024-01-20    | 2024-02-15 | 26                    |
| 103      | 1           | 2024-02-15    | NULL       | NULL                  |
| 104      | 2           | 2024-01-05    | 2024-02-10 | 36                    |
| 105      | 2           | 2024-02-10    | NULL       | NULL                  |
| 106      | 3           | 2024-01-15    | 2024-02-20 | 36                    |
| 107      | 3           | 2024-02-20    | NULL       | NULL                  |
| 108      | 4           | 2024-03-01    | NULL       | NULL                  |

**What happened?**
- Customer 1's first gap: 10 days (Jan 10 → Jan 20)
- Customer 1's second gap: 26 days (Jan 20 → Feb 15)
- Customer 2's gap: 36 days

---

### Step 4: Calculate Average Days for Each Customer

```sql
SELECT 
    customer_id,
    AVG(days_until_next_order) AS average_days
FROM (
    SELECT 
        customer_id,
        DATEDIFF(
            DAY,
            order_date,
            LEAD(order_date) OVER (
                PARTITION BY customer_id 
                ORDER BY order_date
            )
        ) AS days_until_next_order
    FROM sales_orders
) AS order_gaps
GROUP BY customer_id;
```

**Output:**
| customer_id | average_days |
|-------------|--------------|
| 1           | 18.0         |
| 2           | 36.0         |
| 3           | 36.0         |
| 4           | NULL         |

**What happened?**
- **Customer 1**: (10 + 26) ÷ 2 = 18 days average ⭐ Most loyal!
- **Customer 2**: 36 days average
- **Customer 3**: 36 days average
- **Customer 4**: Only 1 order, so NULL

---

### Step 5: Rank Customers (Final Solution!)

```sql
SELECT 
    customer_id,
    COALESCE(average_days, 999) AS average_days,
    RANK() OVER (ORDER BY COALESCE(average_days, 999)) AS loyalty_rank
FROM (
    SELECT 
        customer_id,
        AVG(days_until_next_order) AS average_days
    FROM (
        SELECT 
            customer_id,
            DATEDIFF(
                DAY,
                order_date,
                LEAD(order_date) OVER (
                    PARTITION BY customer_id 
                    ORDER BY order_date
                )
            ) AS days_until_next_order
        FROM sales_orders
    ) AS order_gaps
    GROUP BY customer_id
) AS customer_averages
ORDER BY loyalty_rank;
```

**Final Output:**
| customer_id | average_days | loyalty_rank |
|-------------|--------------|--------------|
| 1           | 18.0         | 1            |
| 2           | 36.0         | 2            |
| 3           | 36.0         | 2            |
| 4           | 999.0        | 4            |

**What happened?**
- 🥇 **Customer 1 = Rank 1**: Orders every 18 days (Very Loyal!)
- 🥈 **Customers 2 & 3 = Rank 2**: Both order every 36 days
- 🥉 **Customer 4 = Rank 4**: Only ordered once (999 is a placeholder)

---

## Understanding COALESCE

**COALESCE** replaces NULL with another value:

```sql
COALESCE(average_days, 999)
```

This means: "If average_days is NULL, use 999 instead"

**Why 999?** 
- We want customers with only 1 order to rank LAST
- 999 days is a very long gap, so they get the lowest rank

---

## Real-World Business Insights

### From Our Analysis:

✅ **Customer 1 is SUPER LOYAL**
- Orders every 18 days on average
- Give them special discounts!
- Send them exclusive offers!

⚠️ **Customers 2 & 3 need attention**
- They wait 36 days between orders
- Maybe send reminder emails?
- Offer limited-time deals?

❌ **Customer 4 is at risk**
- Only ordered once
- Might need a "come back" discount
- Send a follow-up email!

---

## Key SQL Concepts Used

1. **LEAD Function**: Get the next order date
2. **PARTITION BY**: Analyze each customer separately
3. **DATEDIFF**: Calculate days between two dates
4. **AVG**: Find average days
5. **RANK**: Rank customers by loyalty
6. **COALESCE**: Handle NULL values

---

## Practice Exercise

Create your own customer orders table with:
- At least 5 customers
- Multiple orders per customer at different dates
- Use this query to find your most loyal customer!

**Bonus Challenge**: Modify the query to find customers who haven't ordered in over 60 days (they might need a reminder!) 🎯
