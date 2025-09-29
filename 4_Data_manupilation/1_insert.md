## Class Notes: INSERT Command - Adding Data to Tables

### 1. What is INSERT Command?

**INSERT** is a DML (Data Manipulation Language) command used to **add new rows of data** to a table. Think of it like adding new entries to a notebook - the notebook (table) already exists, now we're filling it with information!

**Important Difference**:
- **DDL commands** (CREATE, ALTER, DROP) change the structure (the container)
- **DML commands** (INSERT) change the data (what goes inside the container)

> - INSERT అనేది DML (Data Manipulation Language) command, tableలో కొత్త rows add చేయడానికి వాడతాం. notebook already ఉన్నట్టు table కూడా ఉంది, ఇప్పుడు దానిలో information భర్తీ చేస్తున్నాం అని అనుకోండి. ముఖ్య తేడా: DDL commands (CREATE, ALTER, DROP) structure (container) మార్చతాయి, DML commands (INSERT) ఆ containerలోని dataని మార్చతాయి.

---

### 2. When Do We Use INSERT?

- When you have an empty table and want to add data
- When you want to add more rows to a table that already has data
- SQL automatically adds new rows at the end of the table

> - INSERT command మనం వాడేది: table ఖాళీగా ఉన్నప్పుడు data add చేయడానికి, లేదా ఇప్పటికే data ఉన్న tableలో కొత్త rows add చేయడానికి. SQL కొత్త rows table చివరలోనే automatically add చేస్తుంది.

---

## Method 1: Manual INSERT (Typing Values Yourself)

### 3. Basic INSERT Syntax

```sql
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3)
```

**Breaking it down**:
1. `INSERT INTO` - Command to add data
2. `table_name` - Which table to add data to
3. `(column1, column2...)` - List of columns you're filling
4. `VALUES` - Keyword before the actual data
5. `(value1, value2...)` - The actual data to insert

### 4. Example: Adding One Customer

```sql
INSERT INTO customers (ID, first_name, country, score)
VALUES (6, 'Anna', 'USA', NULL)
```

**What this does**:
- Adds a new customer with ID 6
- Name is Anna
- Country is USA
- Score is NULL (we don't know it yet)

**Result**: "1 row affected" message

---

Basic INSERT syntax ఇలా ఉంటుంది:

```sql
INSERT INTO table_name (column1, column2, column3)  
VALUES (value1, value2, value3)  
```

అర్థం: INSERT INTO – data add చేయడానికి command, table_name – data ఏ tableకి add చేయాలో, (column1, column2…) – data ఎక్కడ చేర్చాలో columns, VALUES – data ముందే keyword, (value1, value2…) – నిజమైన data.

ఉదాహరణకి, ఒక కొత్త customer add చేయడం:

```sql
INSERT INTO customers (ID, first_name, country, score)  
VALUES (6, 'Anna', 'USA', NULL)  
```

ఇది ID 6, name Anna, country USA, score ఇంకా తెలియదు (NULL)తో ఒక కొత్త customer add చేస్తుంది. Result: "1 row affected" message వస్తుంది.

---

### 5. Inserting Multiple Rows at Once

```sql
INSERT INTO customers (ID, first_name, country, score)
VALUES 
    (6, 'Anna', 'USA', NULL),
    (7, 'Sam', NULL, 100)
```

**Notice**:
- Use comma (`,`) to separate multiple rows
- Each row is in its own parentheses
- Sam's country is NULL (unknown), but score is 100

**Result**: "2 rows affected" message

---

ఒకేసారి multiple rows insert చేయాలంటే:

```sql
INSERT INTO customers (ID, first_name, country, score)  
VALUES  
    (6, 'Anna', 'USA', NULL),  
    (7, 'Sam', NULL, 100)  
```

గమనించండి: multiple rows ని comma (,) తో వేరు చేయాలి, ప్రతి row తన parentheses లో ఉండాలి. ఇక్కడ Sam country తెలియదు (NULL), కానీ score 100. Result: "2 rows affected" message వస్తుంది.

---

---

### 6. Important Rules for INSERT

**Rule 1: Number of columns must match number of values**
```sql
-- CORRECT ✓
INSERT INTO customers (ID, first_name, country)
VALUES (8, 'Max', 'Germany')  -- 3 columns, 3 values

-- WRONG ✗
INSERT INTO customers (ID, first_name, country)
VALUES (8, 'Max')  -- 3 columns, only 2 values = ERROR!
```

**Rule 2: Order matters!**
```sql
-- CORRECT ✓
INSERT INTO customers (ID, first_name, country)
VALUES (8, 'Max', 'Germany')

-- WRONG (but database accepts it!) ✗
INSERT INTO customers (ID, first_name, country)
VALUES (8, 'USA', 'Max')  -- Name and country are switched!
```
*Result*: A customer named "USA" from country "Max" - Wrong but no error!

**Rule 3: Data types must match**
```sql
-- WRONG ✗
INSERT INTO customers (ID, first_name, country)
VALUES ('Max', 9, 'Germany')  -- ID should be number, not text!
```
*Result*: Database will give an error

**Rule 4: NOT NULL columns must have values**
```sql
-- WRONG ✗
INSERT INTO customers (ID, first_name, country)
VALUES (NULL, 'Max', 'Germany')  -- ID is NOT NULL, can't be empty!
```
*Result*: Database will give an error

---

### 7. Three Ways to Write INSERT

**Way 1: List all columns** (Safest, recommended)
```sql
INSERT INTO customers (ID, first_name, country, score)
VALUES (9, 'Andreas', 'Germany', 50)
```

**Way 2: Skip column list if inserting all columns in correct order**
```sql
INSERT INTO customers
VALUES (9, 'Andreas', 'Germany', 50)
```
*Note*: Must include values for ALL columns in the EXACT order they appear in the table

**Way 3: List only some columns** (Others become NULL)
```sql
INSERT INTO customers (ID, first_name)
VALUES (10, 'Sarah')
```
*Result*: Sarah's country and score will be NULL (empty)

**Warning**: You can only skip columns that allow NULL values!

> - కొన్ని columns మాత్రమే insert చేయాలంటే, మిగతా columns NULL అవుతాయి:

```sql
INSERT INTO customers (ID, first_name)  
VALUES (10, 'Sarah')  
```

> - ఇది insert చేసినప్పుడు Sarah column values లో country మరియు score NULL (empty) అవుతాయి. గమనిక: NULL values allow చేసిన columns మాత్రమే skip చేయవచ్చు.

---

### 8. Understanding NULL Values

**What is NULL?**
- NULL means "no data" or "unknown"
- It's NOT the same as 0 or empty text ""
- It means "we don't have this information"

**Example**:
```sql
INSERT INTO customers (ID, first_name, country, score)
VALUES (6, 'Anna', 'USA', NULL)
```
Anna's score is NULL = we don't know her score yet

> - NULL అంటే ఏమిటంటే “data లేదు” లేదా “తెలియదు” అని. ఇది 0 లేదా empty text "" కాదని గమనించాలి. అంటే మనకు ఆ సమాచారం ఇంకా లేదు అని అర్థం. ఉదాహరణకి:

```sql
INSERT INTO customers (ID, first_name, country, score)  
VALUES (6, 'Anna', 'USA', NULL)  
```

> - ఇక్కడ Anna score NULL = మనకు ఆమె score ఇంకా తెలియదు.

---

### 9. INSERT Output Messages

**After INSERT, you get a message, NOT data**:
- "2 rows affected" - You inserted 2 rows
- This is different from SELECT which shows you data

**To see your inserted data**:
```sql
SELECT * FROM customers
```

---

## Method 2: INSERT from Another Table

### 10. What Does This Mean?

Instead of typing values manually, we can **copy data from one table to another table**.

**Example scenario**:
- Source table: `customers` (has data)
- Target table: `persons` (empty, needs data)
- Goal: Copy customer information into persons table

> - మనం values ని మానవీయంగా type చేయడం కాకుండా, ఒక table నుండి data copy చేసి మరొక table లో insert చేయవచ్చు. ఉదాహరణ: source table – customers (data already ఉంది), target table – persons (ఖాళీ, data కావాలి). మన లక్ష్యం: customers table లోని సమాచారం persons table కి copy చేయడం.

### 11. Two-Step Process

**Step 1**: Write a SELECT query to get data from source table
```sql
SELECT ID, first_name, NULL, 'unknown'
FROM customers
```

**Step 2**: Add INSERT INTO before the SELECT
```sql
INSERT INTO persons (ID, person_name, birth_date, phone)
SELECT ID, first_name, NULL, 'unknown'
FROM customers
```

> - ఇది రెండు-పదుల ప్రక్రియ: Step 1 – source table నుండి data select చేయడం:

```sql
SELECT ID, first_name, NULL, 'unknown'  
FROM customers  
```

> - Step 2 – SELECT ముందు INSERT INTO add చేయడం:

```sql
INSERT INTO persons (ID, person_name, birth_date, phone)  
SELECT ID, first_name, NULL, 'unknown'  
FROM customers  
```

> - అర్థం: customers table నుండి data select చేసి, persons table లో insert చేయడం.


### 12. Complete Example with Explanation

**Target table structure** (persons):
- ID (int, NOT NULL)
- person_name (varchar, NOT NULL)
- birth_date (date, can be NULL)
- phone (varchar, NOT NULL)

**Source table** (customers) has:
- ID ✓
- first_name ✓
- country (don't need)
- score (don't need)

**Our INSERT query**:
```sql
INSERT INTO persons (ID, person_name, birth_date, phone)
SELECT 
    ID,              -- Copy ID directly
    first_name,      -- Copy first_name as person_name
    NULL,            -- No birth_date data, use NULL
    'unknown'        -- No phone data, use default text 'unknown'
FROM customers
```

**Result**: "10 rows affected" - copied 10 customers to persons table

> - Target table persons structure: ID (int, NOT NULL), person_name (varchar, NOT NULL), birth_date (date, NULL అవ్వచ్చు), phone (varchar, NOT NULL). Source table customers లో ID, first_name ఉన్నవి; country, score మనకు అవసరం లేదు. INSERT query:

```sql
INSERT INTO persons (ID, person_name, birth_date, phone)  
SELECT  
    ID,           -- ID కాపీ చేయడం  
    first_name,   -- first_name ను person_name గా కాపీ చేయడం  
    NULL,         -- birth_date data లేనందున NULL వాడడం  
    'unknown'     -- phone data లేనందున default 'unknown' వాడడం  
FROM customers  
```

> - Result: "10 rows affected" – 10 customers persons table లో copy అయ్యాయి.


---

### 13. Important Points for INSERT...SELECT

**Point 1**: Column names don't need to match
- Source has `first_name`, target has `person_name` - that's okay!
- Database only cares that data types match

**Point 2**: Number of columns must match
- If target has 4 columns, your SELECT must return 4 columns

**Point 3**: You can use static values
- `NULL` for missing data (if allowed)
- `'unknown'` for default text
- `0` for default numbers

**Point 4**: Data types must be compatible
- int → int ✓
- varchar → varchar ✓
- int → varchar ✗ (usually error)

> - INSERT…SELECT లో ముఖ్యమైన విషయాలు: Column names match కావలసిన అవసరం లేదు – source లో first_name, target లో person_name ఉన్నా సరిపోతుంది, databaseకి data types match అవుతాయా అన్నే ముఖ్యం. Columns సంఖ్య match అవ్వాలి – target 4 columns ఉన్నా SELECT 4 columns return చేయాలి. Static values వాడవచ్చు – missing data కోసం NULL (అనుమతించబడితే), default text కోసం 'unknown', default numbers కోసం 0. Data types compatible ఉండాలి – int → int ✓, varchar → varchar ✓, int → varchar ✗ (సాధారణంగా error).

---

### 14. Comparing Both INSERT Methods

| Feature | Method 1: Manual VALUES | Method 2: INSERT...SELECT |
|---------|------------------------|---------------------------|
| **When to use** | Adding a few rows | Copying many rows |
| **Data source** | You type it | From another table |
| **Speed** | Slow for many rows | Fast for many rows |
| **Example** | Adding 1-5 customers | Copying 1000 customers |

> - రెండు INSERT methods తేడాలు: Method 1 (Manual VALUES) – కొద్దీ rows add చేయడానికి, data మీరు manually type చేయాలి, చాలా rows కోసం slow. Method 2 (INSERT…SELECT) – ఎక్కువ rows copy చేయడానికి, data మరో table నుండి వస్తుంది, ఎక్కువ rows కోసం fast. ఉదాహరణ: 1-5 customers add చేయడం vs 1000 customers copy చేయడం.

---

### 15. Practice Examples

**Example 1: Insert one student**
```sql
INSERT INTO students (ID, name, grade, age)
VALUES (1, 'John', 7, 13)
```

**Example 2: Insert multiple students**
```sql
INSERT INTO students (ID, name, grade, age)
VALUES 
    (2, 'Mary', 7, 12),
    (3, 'Tom', 7, 13),
    (4, 'Lisa', 7, 12)
```

**Example 3: Insert with some NULLs**
```sql
INSERT INTO students (ID, name, grade)
VALUES (5, 'Bob', 7)
-- Age will be NULL
```

**Example 4: Copy from another table**
```sql
INSERT INTO active_students (ID, name, grade)
SELECT ID, name, grade
FROM students
WHERE grade = 7
```

---

### 16. Common Mistakes to Avoid

❌ **Mistake 1**: Wrong order of values
```sql
-- Country and name are swapped!
-- Country మరియు name స్థానాలు మార్చి పెట్టబడ్డాయి!
INSERT INTO customers (ID, first_name, country)
VALUES (8, 'USA', 'Max')
```

❌ **Mistake 2**: Missing required values
```sql
-- ID is NOT NULL, can't skip it!
-- ID NOT NULL ఉంది, కాబట్టి దానిని skip చేయలేరు!
INSERT INTO customers (first_name, country)
VALUES ('Max', 'USA')
```

❌ **Mistake 3**: Wrong number of values
```sql
-- 3 columns but only 2 values!
INSERT INTO customers (ID, first_name, country)
VALUES (8, 'Max')
```

❌ **Mistake 4**: Wrong data type
```sql
-- ID should be number, not text!
INSERT INTO customers (ID, first_name)
VALUES ('hello', 'Max')
```

---

### 17. Key Vocabulary

- **INSERT**: Command to add new rows to a table
- **VALUES**: Keyword before the actual data
- **NULL**: Means "no data" or "unknown"
- **Rows affected**: How many rows were inserted
- **Source table**: Table where data comes from
- **Target table**: Table where data goes to
- **DML (Data Manipulation Language)**: Commands that change data (INSERT, UPDATE, DELETE)

> - Key Vocabularyలో, INSERT అంటే tableలో కొత్త rows add చేయడానికి command, VALUES అంటే నిజమైన data ముందు వాడే keyword, NULL అంటే “data లేదు” లేదా “తెలియదు”, Rows affected అంటే ఎంత rows insert అయ్యాయో చూపిస్తుంది, Source table అంటే data వస్తున్న table, Target table అంటే data చేరే table, DML (Data Manipulation Language) అంటే table లో data మార్చే commands (INSERT, UPDATE, DELETE).

---

### 18. Quick Tips

✓ Always double-check your column order
✓ Make sure values match the data types
✓ Remember which columns allow NULL and which don't
✓ Use INSERT...SELECT for copying many rows
✓ Use VALUES for adding a few rows manually
✓ Test with SELECT first before doing INSERT...SELECT

> - Quick Tips: ఎప్పుడూ column order double-check చేయండి, values data types కి సరిపోతున్నాయా చూసుకోండి, ఏ columns NULL కి అనుమతించబడతాయో గుర్తుంచుకోండి, ఎక్కువ rows copy చేయడానికి INSERT…SELECT వాడండి, కొద్దీ rows manually add చేయడానికి VALUES వాడండి, INSERT…SELECT run చేయేముందు SELECT తో test చేయండి.

---

### 19. Summary

**Two ways to INSERT data**:

1. **Manual INSERT with VALUES**
   - Type the data yourself
   - Good for small amounts of data
   - Format: `INSERT INTO table (columns) VALUES (data)`

2. **INSERT from SELECT**
   - Copy from another table
   - Good for large amounts of data
   - Format: `INSERT INTO table (columns) SELECT ... FROM source_table`

Both methods add new rows to your table, and both show "X rows affected" instead of returning data!

> - Summary: Data insert చేయడానికి రెండు మార్గాలు ఉన్నాయి: Manual INSERT with VALUES – data మీరు మానవీయంగా type చేయడం, చిన్న data కోసం బాగుంది, format: INSERT INTO table (columns) VALUES (data). INSERT from SELECT – data మరో table నుండి copy చేయడం, పెద్ద data కోసం బాగుంది, format: INSERT INTO table (columns) SELECT … FROM source_table. ఇరువురు methods కొత్త rows table లో add చేస్తాయి, data return చేయకుండా "X rows affected" చూపిస్తాయి.
