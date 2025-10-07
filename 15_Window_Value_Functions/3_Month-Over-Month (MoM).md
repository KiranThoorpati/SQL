# SQL Use Case: Month-Over-Month (MoM) Analysis
### Understanding Business Performance Over Time

## What is Month-Over-Month Analysis?

**Month-Over-Month (MoM)** analysis helps businesses understand how their performance changes from one month to the next. It's like comparing your test scores - did you improve or decline compared to last month?

### Why is it Important?
- 📈 **Track Growth**: See if sales are increasing or decreasing
- 🔍 **Spot Trends**: Identify patterns in business performance
- ⚠️ **Quick Alerts**: Catch problems early (like a sudden drop in sales)

---

## The Problem We're Solving

**Task**: Calculate the percentage change in sales between the current month and the previous month.

---

## Step-by-Step Solution

### Step 1: Create Sample Data

```sql
CREATE TABLE sales_orders (
    order_id INT,
    order_date DATE,
    sales DECIMAL(10,2)
);

INSERT INTO sales_orders VALUES
(1, '2024-01-05', 50.00),
(2, '2024-01-15', 30.00),
(3, '2024-01-25', 25.00),
(4, '2024-02-08', 60.00),
(5, '2024-02-14', 75.00),
(6, '2024-02-20', 60.00),
(7, '2024-03-03', 40.00),
(8, '2024-03-12', 20.00),
(9, '2024-03-18', 20.00);
```

---

### Step 2: Extract Month from Date and Calculate Total Sales

First, we need to find total sales for each month:

```sql
SELECT 
    MONTH(order_date) AS order_month,
    SUM(sales) AS current_month_sales
FROM sales_orders
GROUP BY MONTH(order_date)
ORDER BY order_month;
```

**Output:**
| order_month | current_month_sales |
|-------------|---------------------|
| 1           | 105.00              |
| 2           | 195.00              |
| 3           | 80.00               |

**What happened?**
- January (month 1): 50 + 30 + 25 = 105
- February (month 2): 60 + 75 + 60 = 195
- March (month 3): 40 + 20 + 20 = 80

---

### Step 3: Add Previous Month Sales Using LAG

Now we use the **LAG** function to get the previous month's sales:

```sql
SELECT 
    MONTH(order_date) AS order_month,
    SUM(sales) AS current_month_sales,
    LAG(SUM(sales)) OVER (ORDER BY MONTH(order_date)) AS previous_month_sales
FROM sales_orders
GROUP BY MONTH(order_date)
ORDER BY order_month;
```

**Output:**
| order_month | current_month_sales | previous_month_sales |
|-------------|---------------------|----------------------|
| 1           | 105.00              | NULL                 |
| 2           | 195.00              | 105.00               |
| 3           | 80.00               | 195.00               |

**What happened?**
- January: No previous month, so NULL
- February: Previous month (January) = 105
- March: Previous month (February) = 195

---

### Step 4: Calculate the Difference (Change in Sales)

```sql
SELECT 
    order_month,
    current_month_sales,
    previous_month_sales,
    current_month_sales - previous_month_sales AS month_over_month_change
FROM (
    SELECT 
        MONTH(order_date) AS order_month,
        SUM(sales) AS current_month_sales,
        LAG(SUM(sales)) OVER (ORDER BY MONTH(order_date)) AS previous_month_sales
    FROM sales_orders
    GROUP BY MONTH(order_date)
) AS monthly_data
ORDER BY order_month;
```

**Output:**
| order_month | current_month_sales | previous_month_sales | month_over_month_change |
|-------------|---------------------|----------------------|-------------------------|
| 1           | 105.00              | NULL                 | NULL                    |
| 2           | 195.00              | 105.00               | 90.00                   |
| 3           | 80.00               | 195.00               | -115.00                 |

**What happened?**
- January: No change (first month)
- February: 195 - 105 = +90 (📈 Sales went UP!)
- March: 80 - 195 = -115 (📉 Sales went DOWN!)

---

### Step 5: Calculate Percentage Change

```sql
SELECT 
    order_month,
    current_month_sales,
    previous_month_sales,
    month_over_month_change,
    ROUND(
        (CAST(month_over_month_change AS FLOAT) / previous_month_sales) * 100, 
        1
    ) AS month_over_month_percentage
FROM (
    SELECT 
        order_month,
        current_month_sales,
        previous_month_sales,
        current_month_sales - previous_month_sales AS month_over_month_change
    FROM (
        SELECT 
            MONTH(order_date) AS order_month,
            SUM(sales) AS current_month_sales,
            LAG(SUM(sales)) OVER (ORDER BY MONTH(order_date)) AS previous_month_sales
        FROM sales_orders
        GROUP BY MONTH(order_date)
    ) AS monthly_data
) AS change_data
ORDER BY order_month;
```

**Final Output:**
| order_month | current_month_sales | previous_month_sales | month_over_month_change | month_over_month_percentage |
|-------------|---------------------|----------------------|-------------------------|-----------------------------|
| 1           | 105.00              | NULL                 | NULL                    | NULL                        |
| 2           | 195.00              | 105.00               | 90.00                   | 85.7%                       |
| 3           | 80.00               | 195.00               | -115.00                 | -59.0%                      |

---

## Understanding the Results

### February Performance:
- **+85.7%** means sales increased by almost 86% compared to January
- 🎉 **Great news!** The business is growing

### March Performance:
- **-59.0%** means sales dropped by 59% compared to February
- ⚠️ **Warning!** Something went wrong - maybe investigate why

---

## Key Concepts Explained

### Formula for Percentage Change:
```
Percentage Change = ((Current Month - Previous Month) / Previous Month) × 100
```

**Example (February):**
- Change = 195 - 105 = 90
- Percentage = (90 / 105) × 100 = 85.7%

---

## Why Use LAG Function?

Without LAG, you would need complex **JOIN** operations to compare months. LAG makes it simple:

✅ **With LAG**: One query, easy to read  
❌ **Without LAG**: Multiple joins, complicated code

---

## Real-World Applications

1. **Retail Stores**: Track monthly sales performance
2. **Websites**: Monitor monthly visitor growth
3. **Schools**: Compare test score improvements month by month
4. **YouTube Channels**: Check if subscribers are increasing

---

## Practice Exercise

Create a table with your monthly savings and use this technique to calculate:
- How much more (or less) you saved each month
- The percentage change in your savings

**Try it yourself!** 🚀
