# Joining Multiple Tables in SQL: A Comprehensive Guide

When I write queries for data analysis, I always follow a structured approach. Every analysis has a starting point – a main topic I'm analyzing, like customer data. This means I always have a master table that serves as the foundation of my analysis.

> - I always use a structured approach when writing data analysis queries. Each analysis starts with a main topic, such as customer data, and is based on a master table that forms the foundation of the analysis.

I begin my query with this main table, which I'll call Table A. However, the data in this table is often insufficient on its own. I need additional information from other tables. For instance, Table B might contain supplementary data that enhances my master table. To connect these tables, I use a LEFT JOIN, which allows me to preserve all records from my master table while adding relevant information from Table B.

> - I start my query with the main table, called Table A. Since this table often doesn’t have all the data I need, I add information from other tables. For example, I use a LEFT JOIN to bring in extra data from Table B while keeping all records from Table A.

As my analysis continues, I might discover interesting information in yet another table – Table C. The same process applies: I use another LEFT JOIN to connect it. This pattern repeats as I continue connecting multiple tables to the main table in the center. My query structure consistently features the master table with multiple LEFT JOINs to supporting tables.

> - As the analysis goes on, I may find useful data in another table, like Table C. I connect it the same way, using a LEFT JOIN. This process repeats, with the main table in the center and multiple LEFT JOINs to other tables.

You might wonder why I don't use INNER JOINs when I only want to see matching data. The answer lies in flexibility. I can control exactly what appears in my final results using the WHERE clause. This approach gives me more flexibility to determine whether I want to see matching or non-matching data, similar to what we've done with anti-joins. By starting from the main table, performing LEFT JOINs with all other tables, and using WHERE conditions to control the final results, I maintain maximum control over my analysis.

> - You might ask why I don’t use INNER JOINs. Using LEFT JOINs gives me more flexibility. I start with the main table, join other tables using LEFT JOINs, and then use the WHERE clause to control what data appears in the final result, whether matching or not.
> - మీరు INNER JOINలు ఎందుకు వాడలేదని అడగొచ్చు. LEFT JOINలు వాడితే నాకు ఎక్కువ ఫ్లెక్సిబిలిటీ ఉంటుంది. నేను మెయిన్ టేబుల్‌తో మొదలుపెట్టి, మిగతా టేబుల్‌లను LEFT JOINలతో జాయిన్ చేసి, చివరికి WHERE క్లాజ్ ఉపయోగించి ఫైనల్ రిజల్ట్‌లో ఏ డేటా రావాలో కంట్రోల్ చేస్తాను.


## Visualizing Table Relationships

> - Seeing how tables are connected

If I visualize this structure using circles, it looks like this: Circle A represents the master table, my starting point. I want to see all data from Table A, and I LEFT JOIN it with Table B, from which I only want the matching data. Then I add another circle for Table C, again wanting only the matching data. You can continue adding circles following this same pattern, with each new circle showing only matching data.

> - If I picture this using circles, Circle A is the main table and the starting point. I keep all data from Table A and LEFT JOIN it with Table B to get only matching data. Then I add Table C the same way, and this pattern continues for more tables.

There's an alternative approach if you don't have a clear master table. If all tables are equally important for understanding your topic – say, customer data – you can join all tables using INNER JOINs. This method focuses exclusively on the overlapping data between all tables. Using our circle visualization, you'd only get the section where all three circles overlap. This is another valid way to join multiple tables.

> -If there’s no clear main table, you can use a different approach. When all tables are equally important, you can join them using INNER JOINs. This shows only the data that exists in all tables, like the overlapping part of the circles.

## Practical Exercise: Joining Multiple Tables

> - Hands-on Practice: Combining Multiple Tables

Let me demonstrate with a practical task using the Sales database. We need to retrieve a list of all orders along with their related customer, product, and employee details. For each order, we'll display the Order ID, Customer Name, Product Name, Sales Price, and Salesperson Name.

> - Let’s try a practical example with the Sales database. We want a list of all orders, showing the Order ID, Customer Name, Product Name, Sales Price, and Salesperson Name.

The first step is switching to the correct database. Instead of using our previous database, we execute `USE sales_db;` to connect to the Sales database.

> - First, switch to the Sales database by running `USE sales_db;` instead of the previous database.

Reading through this task, I notice multiple tables are involved: orders, customers, products, and employees – four tables in total. The key question is: which is the main table? The task focuses on "all orders," meaning we cannot miss any order. This tells me that orders is the main table and my starting point. The other information is supplementary, making orders the obvious choice for the master table.

> - This task involves four tables: orders, customers, products, and employees. Since we need to include all orders, the orders table is the main table. The other tables provide extra information.

Let's start with `SELECT * FROM sales.orders`. Note that this database uses a schema prefix – you'll see "sales.tablename" on the left side of the interface. After executing this query, we can examine the orders table. It contains numerous columns and IDs that will help us join with other tables.

> - Start with `SELECT * FROM sales.orders`. This database uses a schema prefix, so tables appear as "sales.tablename." Running this query shows the orders table, which has many columns and IDs for joining with other tables.

From the orders table, we need the Order ID and Sales columns. I'll assign aliases to make the query clearer: `SELECT o.OrderID, o.Sales FROM sales.orders AS o`. This gives us the basic order information.

We need the Order ID and Sales from the orders table. Using aliases, the query is: `SELECT o.OrderID, o.Sales FROM sales.orders AS o`. This gives the basic order info.

## Adding Customer Information

> - Include Customer Details

Next, we need the customer's name, which isn't in the orders table. To find it, I explore other tables. I typically write simple queries for each table: `SELECT * FROM sales.customers`, `SELECT * FROM sales.employees`, and so on for each table in the database.

> - Next, we need the customer’s name, which isn’t in the orders table. I check other tables using simple queries like `SELECT * FROM sales.customers` and `SELECT * FROM sales.employees`.

In the customers table, I find first name and last name columns – exactly what I need. To connect this table with orders, I need a common column, typically an ID. The orders table has a CustomerID, which matches the CustomerID in the customers table.

> - In the customers table, I find the first and last name columns we need. To join it with orders, we use the common column CustomerID, which exists in both tables.

For large projects with hundreds of tables, exploring each one individually becomes impractical. Good projects include an Entity Relationship (ER) diagram showing all tables and their relationships. By consulting this diagram, I can quickly understand that the CustomerID in the orders table is a foreign key referencing the primary key in the customers table.

> - For big projects with many tables, checking each one is impractical. A good project has an ER diagram showing all tables and their links. From it, I can see that CustomerID in orders is a foreign key pointing to the customers table.
> - An ER diagram (Entity-Relationship diagram) is a simple visual map that shows tables in a database and how they are connected. It helps you quickly see which columns link one table to another.

Now I can build the join: `LEFT JOIN sales.customers AS c ON o.CustomerID = c.CustomerID`. I use a LEFT JOIN to guarantee all orders appear in the output. Then I add the customer name columns: `c.FirstName, c.LastName`. Executing this query now shows customer information for each order.

> - Now I join the customers table using:
> - `LEFT JOIN sales.customers AS c ON o.CustomerID = c.CustomerID`.
> - The LEFT JOIN ensures all orders are included. I add the customer name columns `c.FirstName` and `c.LastName`, which shows the customer info for each order.

## Adding Product Information

> - Include Product Details

Next, we need the product name. Checking the products table, I find the product name column. The ER diagram shows I can connect orders and products using ProductID. I add another LEFT JOIN: `LEFT JOIN sales.products AS p ON o.ProductID = p.ProductID`. It's crucial to note that all joins connect back to the main orders table, not to each other. For example, I'm not joining customers with products – I'm always joining with the orders table.

> - Next, we need the product name. The products table has this column, and the ER diagram shows we can join it using ProductID. I add:
> - `LEFT JOIN sales.products AS p ON o.ProductID = p.ProductID`.
>  - All joins connect back to the main orders table, not to each other. For example, customers are not joined directly with products.

Now I can add the product information: `p.Product AS ProductName` and `p.Price`. Executing the query shows product details and prices for each order.

> - Now I add `p.Product AS ProductName` and `p.Price`. Running the query shows the product name and price for each order.

## Adding Employee Information

> - Include Employee Details

Finally, we need the salesperson name. The employees table contains first name and last name columns along with an employee ID. However, the orders table doesn't have an "EmployeeID" column – instead, it has "SalespersonID." Checking the ER diagram confirms that the EmployeeID in the employees table connects to the SalespersonID in the orders table.

> - Finally, we need the salesperson’s name. The employees table has first and last name columns and EmployeeID. The orders table has SalespersonID, which links to EmployeeID in employees, as shown in the ER diagram.

I add the final join: `LEFT JOIN sales.employees AS e ON o.SalespersonID = e.EmployeeID`, then include `e.FirstName, e.LastName` in the SELECT clause.

> - I add the last join:
> - `LEFT JOIN sales.employees AS e ON o.SalespersonID = e.EmployeeID`
> - and include `e.FirstName` and `e.LastName` in the SELECT clause.

## Handling Column Name Conflicts

> - Dealing with Duplicate Column Names

This creates a problem: we now have FirstName and LastName appearing twice – once for customers and once for employees. This ambiguity makes the results confusing. The solution is using descriptive aliases: `c.FirstName AS CustomerFirstName`, `c.LastName AS CustomerLastName`, `e.FirstName AS EmployeeFirstName`, `e.LastName AS EmployeeLastName`. Now the results clearly distinguish between customer and employee names.

> - Now we have a problem: FirstName and LastName appear twice, for customers and employees, which is confusing. We fix this by using aliases:
> - `c.FirstName AS CustomerFirstName`, `c.LastName AS CustomerLastName`, `e.FirstName AS EmployeeFirstName`, `e.LastName AS EmployeeLastName`.
> - This clearly shows which names belong to customers and which to employees.

Another critical point: if you don't use table aliases before column names when multiple tables have the same column name, SQL will return an error. SQL cannot determine which FirstName you're referring to. Always specify the table name or alias before the column name, especially when dealing with duplicate column names across tables.

> - Important: If multiple tables have the same column name, you must use table names or aliases before the column. Otherwise, SQL will give an error because it won’t know which column you mean.

## Key Takeaways

> - Main Points

Pay careful attention to join keys and conditions. When working with many tables and columns, it's easy to specify wrong columns for joins, which can produce nonsensical results. Always double-check that you're using the correct keys to join your tables.

> - Be careful with join keys and conditions. Using the wrong columns can give incorrect results, so always check that you’re joining tables with the right keys.

This is exactly how I join tables: I start with an important master table, LEFT JOIN everything else, and use the WHERE clause to filter results if needed. This approach provides maximum flexibility and control when combining data from multiple tables.

> - This is how I join tables: start with the main table, LEFT JOIN other tables, and use WHERE to filter if needed. This gives full control and flexibility when combining data.

Now that you understand table joins thoroughly, we can move on to the second method of combining data from multiple tables: set operators. These allow us to combine rows from multiple tables in different ways.

> - Now that you understand joins, we can look at the second way to combine tables: set operators, which let us merge rows from multiple tables in different ways.

---

### Reference code:

```sql
-- Switch to the Sales database
USE sales_db;

-- Retrieve all orders with customer, product, and employee details
SELECT 
    o.OrderID,
    o.Sales,
    c.FirstName AS CustomerFirstName,
    c.LastName AS CustomerLastName,
    p.Product AS ProductName,
    p.Price,
    e.FirstName AS EmployeeFirstName,
    e.LastName AS EmployeeLastName
FROM sales.orders AS o
LEFT JOIN sales.customers AS c 
    ON o.CustomerID = c.CustomerID
LEFT JOIN sales.products AS p 
    ON o.ProductID = p.ProductID
LEFT JOIN sales.employees AS e 
    ON o.SalespersonID = e.EmployeeID;
```

---

### Using with **WHERE** clause:

```sql
-- Switch to the Sales database
USE sales_db;

-- Retrieve all orders with customer, product, and employee details
SELECT 
    o.OrderID,
    o.Sales,
    c.FirstName AS CustomerFirstName,
    c.LastName AS CustomerLastName,
    p.Product AS ProductName,
    p.Price,
    e.FirstName AS EmployeeFirstName,
    e.LastName AS EmployeeLastName
FROM sales.orders AS o
LEFT JOIN sales.customers AS c 
    ON o.CustomerID = c.CustomerID
LEFT JOIN sales.products AS p 
    ON o.ProductID = p.ProductID
LEFT JOIN sales.employees AS e 
    ON o.SalespersonID = e.EmployeeID
WHERE 
    o.Sales > 1000                          -- Filter for orders with sales greater than 1000
    AND c.CustomerID IS NOT NULL            -- Only show orders with valid customers
    AND p.ProductID IS NOT NULL             -- Only show orders with valid products
    AND e.EmployeeID IS NOT NULL;           -- Only show orders with valid salespeople
```
