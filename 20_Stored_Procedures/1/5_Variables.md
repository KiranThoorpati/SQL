# Working with Variables in SQL Stored Procedures

Variables are placeholders that store values in memory for use within your stored procedure. Unlike parameters, which are inputs provided from outside the stored procedure by whoever executes it, variables live entirely inside the stored procedure. As developers, we use variables to make our code dynamic and to transfer values from one part of our procedure to another.

## Understanding the Difference Between Variables and Parameters

Parameters represent external input that the stored procedure must adapt to, while variables are internal elements we create to make our code more flexible and functional. This distinction is important because it determines how and where we use each one.

## Practical Example: Creating Dynamic Reports

Consider a scenario where you want to generate a report about total customers, but instead of displaying the results as a table in the output, you want to present them as formatted text messages. For example, you might want messages like "The total customers from Germany equal to 2" and "The average score from Germany is equal to 425."

To achieve this, you can use the T-SQL print statement to display messages after executing the stored procedure. The basic syntax is simple: you write "print" followed by your message in single quotes, ending with a semicolon. However, if you hard-code the values directly into these print statements, the messages will always be static, showing the same numbers even when the underlying data changes. This approach won't work if you're calling the function for different countries like USA instead of Germany.

## Making Messages Dynamic

To make the messages dynamic, you first need to handle the country name. Instead of hard-coding "Germany" in the string, you can use the parameter for country. However, since the parameter needs to be part of the string, you must break up the text using concatenation. You stop the text string, add a plus sign for concatenation, insert your parameter (at country), and then continue with more concatenation for the rest of the message. The color coding in your SQL editor will help you see where strings end and variables begin.

## The Three Steps of Working with Variables

Working with variables involves three distinct steps. First, you must declare your variables to tell SQL about them so it can prepare placeholders in memory. Variable declarations typically go at the start of the stored procedure, immediately after the begin statement. You declare a variable using the "declare" keyword, followed by the variable name (such as at total customers) and its data type. For example, if you're using count star in your query, the result will be an integer, so you declare the variable as integer. You can declare multiple variables in one statement by separating them with commas. For an average calculation, you would declare the variable as float to handle decimal values.

The second step is assigning values to your variables. To get values from a query, you write the variable name followed by an equal sign before the column expression in your select statement. For instance, you would write "at total customers equal count star" in your query. This tells SQL that whatever value the query returns should be stored in your variable. It's important to note that when you use this approach, the query no longer returns results in the output. Its sole purpose becomes assigning values to your variables, so you should remove any column aliases from your select statement.

The third step is using your variables wherever needed in your stored procedure. Variables can be used in print statements, in subsequent queries, or anywhere else you need them. They're particularly useful for passing information from one query to another. In this example, you use the variables in your print statements by replacing the static numbers with the variable names, such as at total customers and at average score.

## Handling Data Type Conversions

One important consideration when using variables in print statements is that everything must be a string. You cannot directly use dates, numbers, floats, or other data types. While a varchar parameter like country works fine, numeric variables like total customers and average score need to be converted. You must cast these variables to varchar or nvarchar using the cast function to prevent SQL from throwing errors. The syntax is straightforward: "cast(variable as varchar)" or "cast(variable as nvarchar)."

## Testing the Results

After updating your stored procedure definition with these changes, you can test it with different parameters. When you execute the procedure with the default parameter value of USA, you'll see that the first query no longer returns output in the results pane. Instead, if you check the messages tab, you'll find your formatted text: "Total customers from USA is equal to 3" and "The average score from USA is equal to 825." When you execute the procedure with Germany as the parameter, the messages update accordingly to show "Total customers from Germany is equal to 2" and "The average score from Germany is equal to 425."

This demonstrates the core purpose of variables in SQL: they hold information in one place so you can reuse it later in different parts of your stored procedure, making your code dynamic and flexible.
