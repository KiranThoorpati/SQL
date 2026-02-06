Until now, we have seen how to create simple tables without using any constraints. Now, let us understand how we can create tables **with constraints**.

ఇప్పటివరకు మనం ఎలాంటి constraints ఉపయోగించకుండా simple tables ఎలా create చేయాలో చూసాం.
ఇప్పుడు, constraints తో tables ఎలా create చేయాలో అర్థం చేసుకుందాం.

So, what are constraints? Constraints are **rules, restrictions, or limitations** applied to a table or to specific columns within a table. These rules help maintain the accuracy, consistency, and integrity of the data stored in the database.

అయితే, constraints అంటే ఏమిటి?
Constraints అనేవి ఒక table కి లేదా ఆ table లోని కొన్ని specific columns కి apply చేసే **rules, restrictions లేదా limitations**.

ఈ rules వల్ల database లో store అయ్యే data సరిగ్గా ఉండేలా, consistent గా ఉండేలా, అలాగే data integrity maintain అయ్యేలా సహాయపడతాయి.

The syntax for creating a table with constraints starts with the `CREATE TABLE` statement, followed by the table name. Inside the parentheses, you define the column name along with its data type, and then you specify the constraint name. You can define one or more constraints for different columns within the same table. Each constraint can be of a different type, depending on the rule you want to enforce.

Constraints తో table create చేయడానికి syntax `CREATE TABLE` statement తో start అవుతుంది, తర్వాత table name వస్తుంది.
Parentheses లో, ముందు column name ని దానికి సంబంధించిన data type తో define చేస్తాం, ఆ తర్వాత constraint name ని specify చేస్తాం.

ఒకే table లో different columns కి ఒకటి కంటే ఎక్కువ constraints define చేయొచ్చు.
మీరు apply చేయాలనుకున్న rule మీద ఆధారపడి, ప్రతి constraint ఒక different type లో ఉండొచ్చు.

There are several types of constraints available in SQL Server. The first one is the **NOT NULL constraint**. If you do not want a particular column to contain NULL values, then you apply the NOT NULL constraint to that column.

SQL Server లో చాలా రకాల constraints అందుబాటులో ఉంటాయి. అందులో మొదటిది **NOT NULL constraint**.

ఒక particular column లో NULL values ఉండకూడదనుకుంటే, ఆ column కి NOT NULL constraint ను apply చేస్తారు.

The next type is the **UNIQUE constraint**. This constraint ensures that a column, or a combination of columns, contains only unique values. If you want a particular column or a set of columns in a table to have distinct values with no duplicates, you can define them using the UNIQUE constraint.

తర్వాతి type **UNIQUE constraint**.
ఈ constraint వల్ల ఒక column లో లేదా columns combination లో values అన్నీ unique గా ఉండేలా చూస్తుంది.

ఒక table లోని particular column లేదా కొన్ని columns లో duplicate values ఉండకూడదనుకుంటే, వాటిని UNIQUE constraint తో define చేయొచ్చు.

Another important constraint is the **CHECK constraint**. This is used when you want to enforce a specific condition on a column. For example, if you want to allow only a certain range or specific set of values in a column, you can use a CHECK constraint.

మరొక ముఖ్యమైన constraint **CHECK constraint**.
ఇది ఒక column మీద specific condition apply చేయాలనుకున్నప్పుడు ఉపయోగిస్తారు.

ఉదాహరణకు, ఒక column లో ఒక particular range లోనే values ఉండాలి లేదా కొన్ని specific values మాత్రమే allow చేయాలంటే, CHECK constraint ను ఉపయోగించొచ్చు.

Next is the **DEFAULT constraint**. If no value is provided for a column during data insertion, the DEFAULT constraint automatically assigns a predefined value to that column.

తర్వాతది **DEFAULT constraint**.
Data insert చేసే సమయంలో ఒక column కి value ఇవ్వకపోతే, DEFAULT constraint ఆ column కి ముందే define చేసిన value ని automatic గా assign చేస్తుంది.

The next two constraints are **PRIMARY KEY** and **FOREIGN KEY**. These are known as **relationship constraints** because they are used to establish a relationship between two tables. Primary key and foreign key constraints are very important concepts in database design, and they will be explained in detail separately.

తర్వాత వచ్చే రెండు constraints **PRIMARY KEY** మరియు **FOREIGN KEY**.
వీటిని **relationship constraints** అంటారు, ఎందుకంటే ఇవి రెండు tables మధ్య relationship establish చేయడానికి ఉపయోగిస్తారు.

Database design లో primary key మరియు foreign key constraints చాలా ముఖ్యమైన concepts. వీటిని తర్వాత separate గా detail గా explain చేస్తాం.

Apart from PRIMARY KEY and FOREIGN KEY constraints, the remaining constraints—NOT NULL, UNIQUE, CHECK, and DEFAULT—will be explained in detail in the upcoming lessons.

PRIMARY KEY మరియు FOREIGN KEY constraints కాకుండా, మిగతా constraints అయిన **NOT NULL, UNIQUE, CHECK, DEFAULT** గురించి రాబోయే lessons లో detail గా explain చేస్తాం.

Thank you for joining.
Take care. Bye. 😊
