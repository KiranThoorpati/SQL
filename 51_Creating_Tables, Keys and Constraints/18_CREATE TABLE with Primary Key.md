In this lesson, we are going to discuss a very important concept known as the **Primary Key**. A primary key is used when you want to **uniquely identify each record**, meaning each row, in a table. It ensures that every record in the table can be uniquely distinguished from all others.

ఈ lesson లో మనం **Primary Key** అనే చాలా ముఖ్యమైన concept గురించి discuss చేయబోతున్నాం.
Primary Key ని ఒక table లోని ప్రతి record (అంటే ప్రతి row) ని uniquely identify చేయడానికి ఉపయోగిస్తారు.

దీని వల్ల table లో ఉన్న ప్రతి record మిగతా అన్ని records నుంచి clear గా, unique గా గుర్తించబడుతుంది.

A primary key does **not allow duplicate values** and also **does not allow NULL values** in the column. It is important not to confuse a primary key with a **UNIQUE constraint**. While both enforce uniqueness, a primary key will never allow NULL values, whereas a UNIQUE constraint can allow NULL values. Also, a table can have multiple UNIQUE constraints, but it can have **only one primary key constraint**.

Primary Key లో **duplicate values** కి అసలు అవకాశం ఉండదు, అలాగే ఆ column లో **NULL values** కూడా allow చేయదు.
Primary Key ని **UNIQUE constraint** తో confuse అవ్వకూడదు.

రెండూ uniqueness enforce చేస్తాయి కానీ, Primary Key అయితే ఎప్పుడూ NULL values ని allow చేయదు. UNIQUE constraint మాత్రం NULL values ని allow చేయొచ్చు.
అలాగే, ఒక table లో multiple UNIQUE constraints ఉండొచ్చు, కానీ **ఒకటే Primary Key constraint** మాత్రమే ఉండగలదు.

The main purpose of using a primary key is to maintain **entity integrity**. Entity integrity helps in relating multiple tables by connecting them through specific columns. This relationship can be understood as a **parent–child relationship**, which will be demonstrated in upcoming lessons. The best way to achieve entity integrity is by defining a primary key on one table and linking it to another table using a **foreign key**. The concept of foreign keys will be explained in later lessons. For now, the focus is entirely on understanding primary keys.

Although a table can have only one primary key, it is not mandatory that the primary key consists of only one column. Sometimes, **two or more columns together can form a primary key**, which is known as a composite primary key. In Microsoft SQL Server, if you see a **key symbol** at the beginning of a column, it indicates that the column is either a primary key or part of a unique key constraint.

The syntax for creating a primary key starts with the `CREATE TABLE` statement, followed by the table name. Inside the parentheses, you define all the columns along with their data types. If a particular column needs to be defined as a primary key, you simply add the keyword **PRIMARY KEY** to that column definition.

For example, consider creating a table to store product information. Suppose the table has columns such as **StoreID**, **StoreName**, and **ProdID**. If StoreID is defined as the primary key, it means that StoreID will always contain unique values and will never allow NULL values. The StoreName column can be defined using `VARCHAR(40)` with a NOT NULL constraint, and ProdID can be defined as an integer with a NOT NULL constraint.

In this case, the table contains three columns, and StoreID acts as the primary key. Since only one primary key is allowed per table, StoreID uniquely identifies each row.

Now consider another scenario where a primary key is created using **multiple columns**. In this case, StoreID and ProdID together form the primary key. This means that the combination of StoreID and ProdID must be unique, even though each column individually may contain duplicate values.

While creating such a primary key, you can also explicitly define a **constraint name** using the `CONSTRAINT` keyword. A common naming convention is to start the primary key constraint name with the prefix **PK**, followed by the table name. For example, `PK_StoreProds`. Naming constraints is optional, but it is considered a good practice, especially for readability and maintenance.

After executing the table creation command, SQL Server successfully creates the table. When you refresh the database objects and expand the table, you will notice a **PK label and a key icon** next to the columns that are part of the primary key. This visually indicates that the column is part of a primary key constraint.

In the case of a composite primary key, you may see the key symbol next to more than one column. This does not mean there are multiple primary keys. It simply means that **multiple columns together make up a single primary key**. Since primary key columns cannot contain NULL values, SQL Server automatically enforces the NOT NULL constraint on them.

You can also view the primary key details under the **Keys** section of the table. Here, SQL Server displays the name of the primary key constraint. If you did not explicitly provide a name, SQL Server automatically generates one.

With this, you should now have a clear understanding of what a **primary key** is and how it works. As we move forward, the concept will become even clearer when we discuss relationships between tables.

Thank you for joining, and I’ll see you in the next lesson.
Take care. Bye 😊
