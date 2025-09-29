# SQL MIN and MAX Functions - Easy Notes

## What are MIN and MAX?

**MIN** and **MAX** are special functions in SQL that help us find:
- **MIN** = Finds the **smallest** (lowest) number in a group
- **MAX** = Finds the **biggest** (highest) number in a group

Think of it like finding the shortest and tallest person in your class!

---

## How Do They Work?

### Basic Structure:
```
MIN(column_name) OVER (PARTITION BY group_column)
MAX(column_name) OVER (PARTITION BY group_column)
```

**What does this mean?**
- **MIN/MAX** = What you want to find (smallest or biggest)
- **OVER** = Look at a specific group of data
- **PARTITION BY** = Divide data into groups (like separating boys and girls)

---

## Example 1: Finding Lowest and Highest Sales

Imagine you have a store selling caps and gloves:

**Sales Data:**
- Caps: 20, 10, 5
- Gloves: 30, 70, null

### Finding LOWEST sales for each product:
- For **Caps**: The lowest sale is **5**
- For **Gloves**: The lowest sale is **30** (null is ignored!)

### Finding HIGHEST sales for each product:
- For **Caps**: The highest sale is **20**
- For **Gloves**: The highest sale is **70**

---

## ⚠️ Important: Watch Out for NULL!

**NULL** means "no value" or "empty."

- MIN and MAX **ignore NULL** by default
- BUT if you replace NULL with 0, then 0 becomes the minimum!

**Example:**
- Gloves sales: 30, 70, NULL
  - Lowest = 30 (NULL ignored)
- If we change NULL to 0:
  - Gloves sales: 30, 70, 0
  - Lowest = 0 (now 0 is counted!)

**Lesson:** Always understand what NULL means in your data!

---

## Real Examples

### Example 1: Find Highest and Lowest Sales of ALL Orders

**Question:** What's the biggest and smallest sale ever?

**SQL Code:**
```sql
SELECT 
    order_id,
    sales,
    MAX(sales) OVER () AS highest_sales,
    MIN(sales) OVER () AS lowest_sales
FROM sales_orders;
```

**Result:** 
- Highest sale = 90
- Lowest sale = 10

---

### Example 2: Find Highest and Lowest Sales for EACH Product

**Question:** What's the biggest and smallest sale for each product separately?

**SQL Code:**
```sql
SELECT 
    product_id,
    sales,
    MAX(sales) OVER (PARTITION BY product_id) AS highest_sales_by_product,
    MIN(sales) OVER (PARTITION BY product_id) AS lowest_sales_by_product
FROM sales_orders;
```

**Result:**
- Product 101: Highest = 90, Lowest = 10
- Product 102: Highest = 60, Lowest = 15
- Product 103: Highest = 60, Lowest = 60 (only one sale!)

---

## Cool Use Case: Finding Top Employees

**Question:** Show me only the employee with the highest salary.

**Step 1:** Find the highest salary
```sql
SELECT 
    employee_name,
    salary,
    MAX(salary) OVER () AS highest_salary
FROM employees;
```

**Step 2:** Filter to show only employees with that highest salary
```sql
SELECT * FROM (
    SELECT 
        employee_name,
        salary,
        MAX(salary) OVER () AS highest_salary
    FROM employees
) 
WHERE salary = highest_salary;
```

**Result:** Shows only Michael with 90K salary!

---

## Comparison Analysis: How Far From the Extremes?

**Question:** How far is each sale from the lowest and highest sale?

### Distance from LOWEST sale:
```sql
SELECT 
    sales,
    sales - MIN(sales) OVER () AS deviation_from_min
FROM sales_orders;
```

**What it means:**
- If result = 0, you're at the lowest point!
- If result = 80, you're far away from the lowest

### Distance from HIGHEST sale:
```sql
SELECT 
    sales,
    MAX(sales) OVER () - sales AS deviation_from_max
FROM sales_orders;
```

**What it means:**
- If result = 0, you're at the highest point!
- If result = 80, you're far away from the highest

---

## Key Points to Remember

1. **MIN** finds the smallest number, **MAX** finds the biggest number
2. Use **PARTITION BY** to divide data into groups
3. **NULL values are ignored** unless you replace them
4. You can compare current values with MIN/MAX to see how far they are from extremes
5. MIN and MAX are powerful for analyzing your data!

---

## Practice Questions

1. What does MIN do?
2. What does MAX do?
3. What happens to NULL values?
4. How do you find the highest value for each group?
5. Why is it useful to compare values with MIN and MAX?

**Remember:** These functions help you understand your data better by finding the extremes (smallest and biggest values)!
