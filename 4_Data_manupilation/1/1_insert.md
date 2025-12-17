Here's the transcript written in proper paragraphs:

**Inserting Data into Database Tables**

Now we're going to modify and manipulate data inside the database. Sometimes you have a table inside your database and the table is empty without any rows or data. In order to add data to the table, you can use the INSERT command, which adds new rows to your table. Of course, the table doesn't always have to be empty to add data. You can add new rows to already existing data, and SQL will append it at the end of the table.

**Two Methods for Inserting Data**

There are two methods for inserting new data into the target table. The first and classical way is to use the INSERT command and manually specify the values that should be inserted into the table. You start by specifying the values in the script, and then they're inserted as new rows into the target table. In this process, you are manually inserting new values to the table using an SQL script. We're going to focus on this scenario and how to insert data.

**INSERT Command Syntax**

Let's check the syntax of the INSERT command. You start with the keyword INSERT INTO, followed by the table name where you want to insert data. Then you specify a list of all columns where you're going to insert values. After that, you say VALUES and specify the data that should be inserted to the table as a list, similar to how you listed the columns. In INSERT statements, specifying the columns is totally optional. If you don't specify the columns of the table, SQL expects you to insert values into each column. Sometimes you don't want to insert a value for each column and can skip a few, but if you want to insert a value for each column, you can either specify them as a list or skip it entirely.

There's a very important rule for INSERT statements: the number of columns and values must match. If you specify three columns, you must insert exactly three values. One last thing about the syntax is that you can insert multiple values in one go. For each row, you can specify a list of values that must be inserted.

**Practical Examples**

Let's practice INSERT commands in SQL. To insert new customers, start with INSERT INTO and specify the table name "customers." After that, specify a list of columns where you want to insert data. Check which columns exist inside the table—we have ID, first name, country, and score. Create a list of these columns: ID, first name, country, and score.

Now specify the values that should be inserted. Open parentheses and specify an ID. Since the last customer was five, we'll use customer six. Give the customer a name, let's say "Anna," then a country, "USA." If this customer has no score, use NULL, which means we don't know the score—NULL means nothing or unknown.

To insert a second row or another customer, separate it with a comma and repeat the process. The ID is seven, the name is "Sam," the country is NULL (if we don't know it), but the score is 100. As you can see, we're adding a value for each column, and if you don't know the answer, make it NULL if the database allows it. Some columns are not allowed to be NULL, like the primary key. If you try to insert NULL for the ID, the database will not allow it.

When you execute the command, the output of modification commands always indicates what happened to the data. It says "two rows affected," where "affected" might mean inserted, updated, or deleted. You get a general statement from the database indicating how many records were affected. We got two because we inserted two records. Unlike a query, we're not getting any data in the output, just a message. This is a big difference between querying data using SELECT and modifying data using INSERT—we're doing direct modifications to the data inside our database.

To see the data in the customers table, query the data using SELECT * FROM customers to see the whole table. After executing, you can see we have seven customers, including Anna and Sam. This is how you insert data into the database.

**Important Rules and Considerations**

There are a few rules to be careful about when inserting new data. Pay attention that the order of columns you've defined in the INSERT statement matches the values you're inserting. For example, if you accidentally insert the country in the first name column and the name in the country column, the database might accept it because both are VARCHAR data types. The database doesn't care about the content of the data as long as you're following the data type rules. SQL will insert the data blindly as long as you're following the data type rules and constraints.

However, if you make an error like trying to insert a string in an ID column that expects a number, the database is smart enough to reject your INSERT. Be careful with the order of your columns.

If you're defining all columns in the INSERT command exactly like the table—with complete match and correct order—there's a lazy way where you can remove the column list entirely. The database will understand that you're inserting values to all columns in the correct direction. However, you must ensure the values match the number and order of columns in the table.

You can also specify only certain columns in the definition. If you only know two pieces of information (like ID and name), you don't always have to write NULL for other columns—you can skip them. Define only the columns you want to insert, for example, ID and first name. Just be careful: if you define two columns, the values should also be for two columns only. When you execute this, SQL will automatically set NULL for the other columns you didn't define. However, you cannot skip a column that is not allowed to be NULL. You must always include in your list all columns that have a NOT NULL constraint.

**Second Method: Inserting Data from Another Table**

Now let's move to another method of inserting data, this time not manually but using another table. Imagine we have an already existing table with data—this is the source table. We have another table that is empty, the target table, and we want to insert new data into it. We can take the data from the source table and insert it into the target table without manually writing a script for the values, effectively moving data from one table to another.

To do this, follow two steps. First, write an SQL query using SELECT FROM to select the data you need from the source table. This is like doing a normal query—you write SELECT and get results. Second, take these results and use an INSERT command to insert them into the target table. This way, you've moved the data from the source table to the target table.

**Practical Example: Moving Data Between Tables**

Let's say we need to insert data from the table "customers" into the table "persons." The source table is customers and the target table is persons. Keep your eye on the target table to understand its structure. Looking at the persons table, we see it has ID, person name, birth date, and phone columns. Only birth date accepts NULLs, and the rest require information.

Start by writing the query from the source table: SELECT * FROM customers to get an overview. Next, design a perfect result from this query that matches the target table. We need ID, which we have from the customers table. We need person name, and we have first name from the original table, which is a perfect match. For birth date, we don't have that information, but the database accepts NULL, so write NULL. For phone, we don't have phone information either, but it's marked as NOT NULL, so we must provide a default value. Use a static value like 'unknown' in single quotes since it's VARCHAR.

When you query this, you get ID, first name, birth date as empty, and phone as unknown. You might notice the column names don't match the persons table, but the database doesn't care about that as long as the result data matches the table structure. The database never compares column names together, only the data. You can add aliases exactly like the target table if you like, but it has no effect on the results.

Now you have a SELECT query with results, but this isn't an INSERT yet. To insert the result into the persons table, use INSERT INTO commands. Write INSERT INTO, specify the target table (persons), and list all the column names. While you can skip listing columns if you have an exact match, it's better to always add them to ensure there are no issues. List ID, person name, birth date, and phone, then execute.

When it works, you'll see "ten rows affected," meaning ten rows were inserted from the customers table into the target persons table. Query the persons table with SELECT * FROM persons to verify everything worked perfectly. You'll see the ten persons added from the customers table. This is how you move data from one table and insert it into another table.

It's very simple: first, write a query from the source table to collect the data you need, then insert it into the target table. This is a nice and easy way to insert data into your database.
