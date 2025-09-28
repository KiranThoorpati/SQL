## What is DDL (Data Definition Language)?

**DDL** stands for **Data Definition Language**. Think of it as the set of commands we use to create and manage the structure of a database - like building the blueprint of a house before you can put furniture in it.

DDL commands help us:
- Create new tables (like making new folders)
- Modify existing tables (like adding new sections to folders)
- Delete tables (like removing folders)
- Set up rules for our data

**Important**: DDL commands change the structure of the database, not the data inside it. They don't give you back any data - just a message saying "success" or "failed."

---

## Class Notes: Creating Tables with SQL DDL

### 1. The CREATE TABLE Command

When we want to make a new table, we use the `CREATE TABLE` command. It's like creating a new spreadsheet with specific columns.

**Basic Structure:**
```sql
CREATE TABLE table_name (
    column1_name data_type constraints,
    column2_name data_type constraints,
    ...
)
```

### 2. Example: Creating a "Persons" Table

Let's say we want to create a table to store information about people. We need:
- ID (a unique number for each person)
- Person Name
- Birth Date
- Phone Number

**Step-by-step breakdown:**

```sql
CREATE TABLE persons (
    ID int NOT NULL,
    person_name varchar(50) NOT NULL,
    birth_date date,
    phone varchar(15) NOT NULL,
    CONSTRAINT PK_persons PRIMARY KEY (ID)
)
```

### 3. Understanding Data Types

**Data Types** tell the database what kind of information each column can store:

- **int**: Numbers only (like 1, 2, 100, 500)
- **varchar(number)**: Text with a maximum length
  - varchar(50) means up to 50 characters
- **date**: Dates (like 2023-05-15)

### 4. Understanding Constraints

**Constraints** are rules that control what data can go into each column:

- **NOT NULL**: This column must have a value (cannot be empty)
- **PRIMARY KEY**: A special column that uniquely identifies each row (like a student ID number)

### 5. Column-by-Column Explanation

1. **ID int NOT NULL**: 
   - Stores numbers only
   - Cannot be empty
   - Each person gets a unique number

2. **person_name varchar(50) NOT NULL**:
   - Stores names (text)
   - Maximum 50 characters
   - Cannot be empty (everyone must have a name)

3. **birth_date date**:
   - Stores birth dates
   - Can be empty (some people might not want to share)

4. **phone varchar(15) NOT NULL**:
   - Stores phone numbers (as text because of special characters like +, -, spaces)
   - Maximum 15 characters
   - Cannot be empty

5. **PRIMARY KEY (ID)**:
   - Makes ID the unique identifier
   - No two people can have the same ID

### 6. What Happens After Creating a Table?

- The database creates an empty table with your specified columns
- You get a success message, not data
- The table has the structure but no information yet
- You can check if it worked by running: `SELECT * FROM persons`

### 7. Important Tips

1. **Save Your Work**: Always save your CREATE TABLE commands in a file so you can use them again later

2. **Getting Your Code Back**: If you lose your original code, you can right-click on the table name and select "Script Table As > CREATE To > New Query" to see the CREATE statement again

3. **Remember**: DDL commands change the database structure, not the data inside it

### 8. Key Vocabulary

- **DDL**: Data Definition Language - commands that change database structure
- **Table**: Like a spreadsheet with rows and columns
- **Column**: A category of information (like "Name" or "Age")
- **Data Type**: What kind of information can go in a column
- **Constraint**: Rules about what data is allowed
- **Primary Key**: A special column that uniquely identifies each row

This is the foundation of creating database tables - once you understand this, you can create tables to store any kind of organized information!
