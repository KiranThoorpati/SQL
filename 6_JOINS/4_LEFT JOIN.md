# SQL LEFT JOIN - Easy Guide for Beginners

## What is a LEFT JOIN?

A **LEFT JOIN** returns ALL rows from the left table and only the MATCHING rows from the right table. If there's no match, you'll see NULL values.

Think of it like this:
- **Left Table** = Your main source (we need EVERYTHING from here)
- **Right Table** = Extra information (we only take what matches)

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/2521b632-65f1-4e41-8ce0-3f65e4855c79" />

## Visual Example

```
Table A (Left)          Table B (Right)
    ●●●●●●●                  ●●●
      FULL CIRCLE          ONLY MATCHING PART
```

## Basic Syntax

```sql
SELECT columns
FROM left_table A
LEFT JOIN right_table B
ON A.key = B.key;
```

⚠️ **Important:** The order of tables matters! Always start with the table you want ALL data from.

<img width="1919" height="1077" alt="image" src="https://github.com/user-attachments/assets/c07bd0dd-1cec-470c-984b-5dd08ba5cef6" />

---

<img width="1918" height="1079" alt="image" src="https://github.com/user-attachments/assets/898680b5-56b2-4f21-b71c-e78834869b23" />


## Real Example: Customers and Orders

### Our Tables

**Customers Table:**
```
CustomerID | FirstName
-----------|----------
1          | John
2          | Sarah
3          | Mike
4          | Martin
5          | Peter
```

**Orders Table:**
```
OrderID | CustomerID | Sales
--------|------------|-------
101     | 1          | 500
102     | 2          | 300
103     | 3          | 450
```

### Task
Get all customers along with their orders, **including those without orders**.

---

### ❌ Wrong Way - INNER JOIN

```sql
SELECT 
    Customers.CustomerID,
    Customers.FirstName,
    Orders.OrderID,
    Orders.Sales
FROM Customers
INNER JOIN Orders
ON Customers.CustomerID = Orders.CustomerID;
```

**Output:**
```
CustomerID | FirstName | OrderID | Sales
-----------|-----------|---------|-------
1          | John      | 101     | 500
2          | Sarah     | 102     | 300
3          | Mike      | 103     | 450
```

**Problem:** Martin and Peter are missing! ❌

---

### ✅ Correct Way - LEFT JOIN

```sql
SELECT 
    Customers.CustomerID,
    Customers.FirstName,
    Orders.OrderID,
    Orders.Sales
FROM Customers
LEFT JOIN Orders
ON Customers.CustomerID = Orders.CustomerID;
```

**Output:**
```
CustomerID | FirstName | OrderID | Sales
-----------|-----------|---------|-------
1          | John      | 101     | 500
2          | Sarah     | 102     | 300
3          | Mike      | 103     | 450
4          | Martin    | NULL    | NULL
5          | Peter     | NULL    | NULL
```

**Success:** All 5 customers are shown! Martin and Peter have NULL because they didn't place any orders. ✅

<img width="1918" height="1076" alt="image" src="https://github.com/user-attachments/assets/7a34e4cf-bb1c-4892-940c-24a5f953d15c" />

---

## How SQL Executes LEFT JOIN (Step-by-Step)

1. **Start with left table** (Customers)
2. **Add each row immediately** to results (no checking needed!)
3. **Search for matching data** in right table (Orders)
4. **If match found** → Add order details
5. **If NO match found** → Add NULL values

### Example Flow:

- **John (ID=1)**: Found in Orders → Shows OrderID 101, Sales 500
- **Sarah (ID=2)**: Found in Orders → Shows OrderID 102, Sales 300
- **Mike (ID=3)**: Found in Orders → Shows OrderID 103, Sales 450
- **Martin (ID=4)**: NOT found in Orders → Shows NULL, NULL
- **Peter (ID=5)**: NOT found in Orders → Shows NULL, NULL

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/d70d2f58-0049-4244-8ce9-bc3ba82757a5" />

---

## Common Mistake: Switching Tables ⚠️

```sql
-- WRONG ORDER!
SELECT *
FROM Orders
LEFT JOIN Customers
ON Orders.CustomerID = Customers.CustomerID;
```

This will show **all orders** but might miss some customers. Not what we wanted!

---

## Key Points to Remember

✅ LEFT JOIN keeps ALL rows from the first table (left table)  
✅ NULL appears when there's no matching data  
✅ Order of tables is VERY important  
✅ Always start FROM the table you want complete data from  
✅ Use it when you have a main table and need extra information from another table  

---

## Practice Question

If a school has:
- **Students table**: 10 students
- **Library Books table**: Only 6 students borrowed books

Which JOIN would you use to see ALL students and show who borrowed books?

**Answer:** LEFT JOIN (starting from Students table) so you don't miss any student! 📚
