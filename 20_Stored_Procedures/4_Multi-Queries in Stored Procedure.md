# Multiple Queries in One Stored Procedure 📊

## What Does "Multiple Queries" Mean?

**Simple Definition:** You can put MORE THAN ONE SQL command inside a single stored procedure!

**Think of it like:** A recipe book page with multiple recipes! 📖
- Recipe 1: How to make a sandwich
- Recipe 2: How to make juice
- Both on the same page, you follow them one by one!

> - “Multiple Queries” అంటే, ఒక్క stored procedure లో ఒక కన్నా ఎక్కువ SQL commands పెట్టుకోవచ్చు! దీన్ని ఇలా ఆలోచించు: ఒక recipe book page లో multiple recipes ఉంటాయి 📖 — Recipe 1: sandwich ఎలా తయారు చేయాలో, Recipe 2: juice ఎలా తయారు చేయాలో, రెండింటినీ ఒక్క page లో sequential గా follow చేయవచ్చు.

---

## Why Use Multiple Queries?

Remember from the beginning? We learned that stored procedures can have MANY SQL statements together!

**Benefits:**
- ✅ Create multiple reports at once
- ✅ Run several tasks together automatically
- ✅ Save time and effort
- ✅ Everything happens in the correct order

> - Multiple Queries ఎందుకు వాడాలి అంటే? మొదట నుండి గుర్తుందా? Stored Procedures లో చాలా SQL statements ఒకేసారి పెట్టుకోవచ్చని నేర్చుకున్నాం!

> - వీటితో లాభాలు:
> - ✅ ఒకేసారి multiple reports create చేయొచ్చు
> - ✅ చాలా tasks ని automatic గా run చేయొచ్చు
> - ✅ సమయం, effort save అవుతుంది
> - ✅ అన్నీ correct order లో జరుగుతాయి

---

## Real Example: Creating Two Reports Together

### **New Task:**
Create a report that shows:
1. **Report 1:** Total number of customers and average score (we already have this!)
2. **Report 2:** Total number of orders and total sales

Instead of creating TWO separate stored procedures, let's put BOTH queries in ONE! 🎯

---

## Step-by-Step: Building Multiple Queries

### **Step 1: Write the Second Query**

First, let's write our new query for orders and sales:

```sql
SELECT COUNT(order_id) AS Total_Orders,
       SUM(sales) AS Total_Sales
FROM sales_orders
JOIN sales_customers 
    ON sales_orders.customer_id = sales_customers.customer_id
WHERE country = 'USA'
```

**What this does:**
- Counts total orders
- Adds up all sales amounts
- Joins two tables (orders + customers)
- Filters by country

**Result for USA:** 6 orders, $180 total sales

> - Step-by-Step: Multiple Queries create చేయడం
> - Step 1: Second Query రాయడం
> - ఇప్పుడు orders & sales కోసం కొత్త query రాయాలి:

```
SELECT COUNT(order_id) AS Total_Orders,
       SUM(sales) AS Total_Sales
FROM sales_orders
JOIN sales_customers 
    ON sales_orders.customer_id = sales_customers.customer_id
WHERE country = 'USA'
```

> - ఇది ఏం చేస్తుందంటే:

> - * Total orders count చేస్తుంది
> - * అన్ని sales amounts add చేస్తుంది
> - * రెండు tables (orders + customers) join చేస్తుంది
> - * country ద్వారా filter చేస్తుంది

> - Result USA కోసం: 6 orders, $180 total sales

---

### **Step 2: Add Both Queries to One Stored Procedure**

Now let's put BOTH queries together!

```sql
ALTER PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'
AS
BEGIN
    -- First Query: Customer statistics
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = @country;
    
    -- Second Query: Order statistics
    SELECT COUNT(order_id) AS Total_Orders,
           SUM(sales) AS Total_Sales
    FROM sales_orders
    JOIN sales_customers 
        ON sales_orders.customer_id = sales_customers.customer_id
    WHERE country = @country;
END
```

---

## Important: Using Semicolons (;) 

### **Best Practice Rule:**
Always put a **semicolon (;)** at the end of each query when you have multiple queries!

```sql
-- Query 1 ends with semicolon
SELECT * FROM table1;

-- Query 2 ends with semicolon
SELECT * FROM table2;
```

### **Why use semicolons?**
- ✅ Shows clearly where one query ends and another begins
- ✅ Makes code easier to read and understand
- ✅ Especially helpful with complex queries (CTEs, UNIONs, etc.)
- ℹ️ Not required by SQL, but it's GOOD PRACTICE!

**Think of it like:** Periods at the end of sentences. Makes everything clearer! 📝

---

## Don't Forget Parameters!

Replace **static values** (like 'USA') with **parameters** (@country):

### **Before (Static - Bad):**
```sql
WHERE country = 'USA'  -- Stuck with USA only!
```

### **After (Dynamic - Good):**
```sql
WHERE country = @country  -- Works for any country!
```

Make sure to change it in **ALL queries** inside the stored procedure!

---

## Executing Multiple Queries

### **Execute for USA (using default):**
```sql
EXECUTE Get_Customer_Summary
```

**Output - You get TWO results:**
1. **Result 1:** Total customers and average score for USA
2. **Result 2:** Total orders and total sales for USA

### **Execute for Germany:**
```sql
EXECUTE Get_Customer_Summary @country = 'Germany'
```

**Output - You get TWO results:**
1. **Result 1:** Total customers and average score for Germany  
2. **Result 2:** Total orders (4) and total sales ($200) for Germany

---

## How SQL Executes Multiple Queries

**Order of Execution:** TOP to BOTTOM ⬇️

```sql
BEGIN
    -- SQL runs THIS first ⬆️
    SELECT ... (Query 1);
    
    -- Then runs THIS second ⬇️
    SELECT ... (Query 2);
    
    -- If there's a third, it runs last ⬇️
    INSERT ... (Query 3);
END
```

**Just like reading a book:** Page 1, then Page 2, then Page 3!

---

## You Can Mix Different SQL Commands! 🎨

You're not limited to just SELECT queries. You can include:

| Command Type | What It Does | Example Use |
|-------------|--------------|-------------|
| **SELECT** | Get data | Generate reports |
| **INSERT** | Add new data | Add new records |
| **UPDATE** | Change existing data | Update customer info |
| **DELETE** | Remove data | Delete old records |

### **Example with Mixed Commands:**
```sql
CREATE PROCEDURE Process_Daily_Tasks
    @country VARCHAR(50)
AS
BEGIN
    -- 1. Get today's orders
    SELECT * FROM orders WHERE order_date = TODAY();
    
    -- 2. Update customer status
    UPDATE customers SET status = 'Active' WHERE country = @country;
    
    -- 3. Insert a log entry
    INSERT INTO logs (action, date) VALUES ('Daily Update', TODAY());
END
```

**All three commands run automatically in order!** 🚀

---

## Complete Working Example

```sql
-- Create/Update the stored procedure
ALTER PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'
AS
BEGIN
    -- Report 1: Customer Statistics
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = @country;
    
    -- Report 2: Order Statistics
    SELECT COUNT(order_id) AS Total_Orders,
           SUM(sales) AS Total_Sales
    FROM sales_orders
    JOIN sales_customers 
        ON sales_orders.customer_id = sales_customers.customer_id
    WHERE country = @country;
END

-- Execute for USA (default)
EXECUTE Get_Customer_Summary;

-- Execute for Germany
EXECUTE Get_Customer_Summary @country = 'Germany';
```

---

## Visual Understanding 🎯

### **One Stored Procedure = Multiple Actions**

```
📦 Stored Procedure Box
├── 📋 Query 1: Customer Report
├── 📋 Query 2: Order Report  
├── 📋 Query 3: Update something
└── 📋 Query 4: Insert a log
```

**Press Execute → All 4 run automatically!**

---

## Key Takeaways 🎓

| Concept | Explanation | Symbol |
|---------|-------------|--------|
| **Multiple Queries** | Many SQL statements in one procedure | 📚 |
| **Semicolon (;)** | Marks the end of each query | ⏹️ |
| **Execution Order** | Top to bottom, one by one | ⬇️ |
| **Multiple Results** | Each SELECT gives separate output | 📊📊 |
| **Mixed Commands** | Can use SELECT, INSERT, UPDATE, DELETE | 🎨 |

---

## Important Rules to Remember 📌

1. **Use semicolons (;)** after each query - good practice!
2. **Replace static values** with parameters (@country)
3. **Queries execute in order** - top to bottom
4. **Each SELECT** produces its own result table
5. **You can mix** different types of SQL commands

---

## Practice Exercise 🎮

**Challenge:** Create a stored procedure that does THREE things:

1. Gets all students from a class
2. Counts total students in that class
3. Shows average marks for that class

```sql
CREATE PROCEDURE Get_Class_Report
    @class_name VARCHAR(50) = '7th Grade'
AS
BEGIN
    -- Query 1: Show all students
    SELECT student_name, marks
    FROM students
    WHERE class = @class_name;
    
    -- Query 2: Count students
    SELECT COUNT(*) AS Total_Students
    FROM students
    WHERE class = @class_name;
    
    -- Query 3: Average marks
    SELECT AVG(marks) AS Average_Marks
    FROM students
    WHERE class = @class_name;
END
```

**Execute it and get all THREE results at once!** 🎉

---

## Remember: 

**Multiple queries in ONE stored procedure = Super efficiency!** 

It's like pressing ONE button and getting multiple things done automatically! 🚀✨
