Until now, we have seen how to create simple tables without using any constraints. Now, let us understand how we can create tables **with constraints**.

So, what are constraints? Constraints are **rules, restrictions, or limitations** applied to a table or to specific columns within a table. These rules help maintain the accuracy, consistency, and integrity of the data stored in the database.

The syntax for creating a table with constraints starts with the `CREATE TABLE` statement, followed by the table name. Inside the parentheses, you define the column name along with its data type, and then you specify the constraint name. You can define one or more constraints for different columns within the same table. Each constraint can be of a different type, depending on the rule you want to enforce.

There are several types of constraints available in SQL Server. The first one is the **NOT NULL constraint**. If you do not want a particular column to contain NULL values, then you apply the NOT NULL constraint to that column.

The next type is the **UNIQUE constraint**. This constraint ensures that a column, or a combination of columns, contains only unique values. If you want a particular column or a set of columns in a table to have distinct values with no duplicates, you can define them using the UNIQUE constraint.

Another important constraint is the **CHECK constraint**. This is used when you want to enforce a specific condition on a column. For example, if you want to allow only a certain range or specific set of values in a column, you can use a CHECK constraint.

Next is the **DEFAULT constraint**. If no value is provided for a column during data insertion, the DEFAULT constraint automatically assigns a predefined value to that column.

The next two constraints are **PRIMARY KEY** and **FOREIGN KEY**. These are known as **relationship constraints** because they are used to establish a relationship between two tables. Primary key and foreign key constraints are very important concepts in database design, and they will be explained in detail separately.

Apart from PRIMARY KEY and FOREIGN KEY constraints, the remaining constraints—NOT NULL, UNIQUE, CHECK, and DEFAULT—will be explained in detail in the upcoming lessons.

Thank you for joining.
Take care. Bye. 😊
