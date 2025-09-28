## Class Notes: Modifying Tables with ALTER Command

### 1. What is the ALTER Command?

**ALTER** is a DDL command that lets you **edit or change** an existing table. Think of it like editing a document - you're not creating a new one, just changing what's already there.

With ALTER, you can:
- Add new columns
- Remove existing columns
- Change column properties

**Remember**: Just like CREATE TABLE, ALTER commands don't return data - they just give you a success or failure message.

---

### 2. Adding a New Column (ALTER TABLE... ADD)

**Task Example**: Add a new column called "email" to the persons table.

**Command Structure**:
```sql
ALTER TABLE table_name
ADD column_name data_type constraints
```

**Our Example**:
```sql
ALTER TABLE persons
ADD email varchar(50) NOT NULL
```

**Step-by-step breakdown**:
1. `ALTER TABLE persons` - We want to change the "persons" table
2. `ADD email` - We're adding a column called "email"
3. `varchar(50)` - The data type (text up to 50 characters)
4. `NOT NULL` - The constraint (email is required)

**What happens**:
- A new "email" column appears in your table
- **Important**: The new column is always added at the **end** of the table
- All existing rows will have empty email fields that need to be filled

### 3. Important Rule About Column Position

**Q**: What if I want the email column in the middle of the table (like after person_name)?

**A**: You **cannot** do this with ALTER! If you want columns in a specific order, you have to:
1. Delete (DROP) the entire table
2. Create it again with CREATE TABLE in the order you want

**Warning**: This means you'll lose all your data! So plan your table structure carefully from the beginning.

### 4. Removing a Column (ALTER TABLE... DROP)

**Task Example**: Remove the "phone" column from the persons table.

**Command Structure**:
```sql
ALTER TABLE table_name
DROP COLUMN column_name
```

**Our Example**:
```sql
ALTER TABLE persons
DROP COLUMN phone
```

**Step-by-step breakdown**:
1. `ALTER TABLE persons` - We want to change the "persons" table
2. `DROP COLUMN phone` - We're removing the "phone" column
3. Notice: We don't need to specify data type or constraints because the database already knows them

**What happens**:
- The "phone" column disappears completely
- **Warning**: All data in that column is permanently lost!

### 5. Before and After Examples

**Original Table Structure**:
- ID (int, NOT NULL, PRIMARY KEY)
- person_name (varchar(50), NOT NULL)
- birth_date (date)
- phone (varchar(15), NOT NULL)

**After Adding Email**:
- ID (int, NOT NULL, PRIMARY KEY)
- person_name (varchar(50), NOT NULL)
- birth_date (date)
- phone (varchar(15), NOT NULL)
- email (varchar(50), NOT NULL) ← **New column at the end**

**After Removing Phone**:
- ID (int, NOT NULL, PRIMARY KEY)
- person_name (varchar(50), NOT NULL)
- birth_date (date)
- email (varchar(50), NOT NULL) ← **Phone column is gone**

### 6. Important Safety Warnings ⚠️

1. **Data Loss**: When you DROP a column, all data in that column is permanently deleted
2. **No Undo**: There's no "undo" button for ALTER commands
3. **Column Order**: New columns always go to the end - you can't choose the position
4. **Plan Ahead**: Think carefully about your table structure before creating it

### 7. When to Use ALTER vs CREATE

**Use ALTER when**:
- You have an existing table with data
- You need to add/remove just a few columns
- You want to keep your existing data

**Use CREATE (after DROP) when**:
- You need columns in a specific order
- You're making major changes to the table structure
- The table is empty or you don't mind losing the data

### 8. Key Vocabulary

- **ALTER**: Command to modify existing table structure
- **ADD**: Adds a new column to a table
- **DROP COLUMN**: Removes a column and all its data
- **Column Position**: Where a column appears in the table (left to right)

### 9. Practice Tips

1. Always double-check your ALTER commands before running them
2. If the table has important data, consider backing it up first
3. Test your changes on a copy of the table if possible
4. Remember: ALTER changes structure, not data content

This is how you can modify your database tables after they're created - but always be careful because these changes are permanent!
