# Creating Triggers - Audit Log Example 📋

## The Use Case: Maintaining Audit Logs

### What is an Audit Log?

**Simple Definition:** An audit log is a record that tracks all changes made to important data - who did what, when, and what changed.

**Think of it like:** A school attendance register that tracks:
- When students arrive
- When they leave
- Who marked them present
- Any changes to their records

---

## Why Do We Need Audit Logs?

### For Employee Table (Example):

The employee table contains **sensitive information**:
- New employees added
- Salary updates
- Employee terminations
- Position changes

**We need to track:**
- **WHEN** did the change happen?
- **WHO** made the change?
- **WHAT** exactly changed?

### Benefits:
- Compliance with rules and regulations
- Investigation when problems occur
- Security and accountability
- Historical tracking

---

## The Solution: Using Triggers

### How It Works:

```
Employee Table
    ↓
Someone INSERTS new employee
    ↓
TRIGGER fires automatically
    ↓
New entry added to Audit Log Table
```

**Result:** Every time someone adds an employee, the log is updated automatically - no manual work needed!

---

## Trigger Syntax (The Rules)

### Basic Structure:

```sql
CREATE TRIGGER trigger_name
ON table_name
AFTER INSERT | UPDATE | DELETE
AS
BEGIN
    -- SQL statements to execute
    -- when trigger fires
END
```

**Breaking it down:**
- `CREATE TRIGGER` = Make a new trigger
- `trigger_name` = Give it a name
- `ON table_name` = Attach to this table
- `AFTER/INSTEAD OF` = When to run
- `INSERT/UPDATE/DELETE` = What event causes it to run
- `BEGIN...END` = What actions to take

---

## Step-by-Step: Building an Audit Log System

### Step 1: Create the Log Table

First, create a table to store all the log entries:

```sql
CREATE TABLE sales_employee_logs (
    log_id INT IDENTITY PRIMARY KEY,
    employee_id INT,
    log_message VARCHAR(255),
    log_date DATETIME
);
```

**Table Structure:**
| Column | Data Type | Purpose |
|--------|-----------|---------|
| `log_id` | INT IDENTITY | Unique ID for each log (auto-increment) |
| `employee_id` | INT | Which employee was affected |
| `log_message` | VARCHAR(255) | Description of what happened |
| `log_date` | DATETIME | When it happened |

---

### Step 2: Create the Trigger

Now create a trigger that logs every new employee insertion:

```sql
CREATE TRIGGER trg_after_insert_employee
ON sales_employee
AFTER INSERT
AS
BEGIN
    -- Insert log entry when new employee is added
    INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
    SELECT 
        employee_id,
        'New employee added - Employee ID: ' + CAST(employee_id AS VARCHAR),
        GETDATE()
    FROM inserted;
END
```

---

## Understanding the Trigger Code

### The Trigger Definition:

```sql
CREATE TRIGGER trg_after_insert_employee
ON sales_employee
AFTER INSERT
```

**Means:**
- Name: `trg_after_insert_employee` (prefix "trg_" indicates it's a trigger)
- Attached to: `sales_employee` table
- Fires: AFTER someone inserts data

---

### The Special "inserted" Table

```sql
FROM inserted
```

**What is "inserted"?**
- A **virtual table** that exists only during trigger execution
- Contains all the data that was just inserted
- Has the same columns as your main table
- Automatically created by SQL

**Important:** You can't query `inserted` outside the trigger - it only exists temporarily!

### Similar Special Tables:
- `inserted` - Contains NEW data (for INSERT and UPDATE)
- `deleted` - Contains OLD data (for DELETE and UPDATE)

---

### Building the Log Message:

```sql
'New employee added - Employee ID: ' + CAST(employee_id AS VARCHAR)
```

**Why CAST?**
- `employee_id` is an INTEGER
- Message must be a STRING
- CAST converts INT to VARCHAR

**Result:** "New employee added - Employee ID: 6"

---

### Using GETDATE():

```sql
log_date = GETDATE()
```

`GETDATE()` returns the current date and time automatically.

---

## Complete Working Example

### The Full Trigger:

```sql
CREATE TRIGGER trg_after_insert_employee
ON sales_employee
AFTER INSERT
AS
BEGIN
    INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
    SELECT 
        employee_id,
        'New employee added - Employee ID: ' + CAST(employee_id AS VARCHAR),
        GETDATE()
    FROM inserted;
END
```

---

## Testing the Trigger

### Before Testing - Check the Log:

```sql
SELECT * FROM sales_employee_logs;
```

**Result:** Empty table (no logs yet)

---

### Insert a New Employee:

```sql
INSERT INTO sales_employee (employee_id, first_name, last_name, position, birth_date, gender, salary, hierarchy)
VALUES (6, 'Maria', 'Garcia', 'HR', '1990-05-15', 'Female', 55000, 3);
```

**What happens automatically:**
1. Maria is inserted into `sales_employee` table
2. Trigger fires immediately
3. Log entry is created in `sales_employee_logs`

---

### Check the Log Again:

```sql
SELECT * FROM sales_employee_logs;
```

**Result:**

| log_id | employee_id | log_message | log_date |
|--------|-------------|-------------|----------|
| 1 | 6 | New employee added - Employee ID: 6 | 2025-09-30 14:30:22 |

**It worked!** The log was created automatically!

---

### Insert Another Employee:

```sql
INSERT INTO sales_employee (employee_id, first_name, last_name, position, birth_date, gender, salary, hierarchy)
VALUES (7, 'John', 'Smith', 'Sales', '1988-08-20', 'Male', 60000, 2);
```

### Check the Log:

| log_id | employee_id | log_message | log_date |
|--------|-------------|-------------|----------|
| 1 | 6 | New employee added - Employee ID: 6 | 2025-09-30 14:30:22 |
| 2 | 7 | New employee added - Employee ID: 7 | 2025-09-30 14:35:45 |

**Every insertion is automatically logged!**

---

## Finding Your Triggers

### In SQL Server Management Studio:

1. Go to **Object Explorer**
2. Expand your database (e.g., `sales_DB`)
3. Expand **Tables**
4. Expand your table (e.g., `sales_employee`)
5. Expand **Triggers** folder
6. You'll see: `trg_after_insert_employee`

---

## Expanding to Other Operations

### Trigger for UPDATE:

```sql
CREATE TRIGGER trg_after_update_employee
ON sales_employee
AFTER UPDATE
AS
BEGIN
    INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
    SELECT 
        employee_id,
        'Employee updated - Employee ID: ' + CAST(employee_id AS VARCHAR),
        GETDATE()
    FROM inserted;
END
```

---

### Trigger for DELETE:

```sql
CREATE TRIGGER trg_after_delete_employee
ON sales_employee
AFTER DELETE
AS
BEGIN
    INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
    SELECT 
        employee_id,
        'Employee deleted - Employee ID: ' + CAST(employee_id AS VARCHAR),
        GETDATE()
    FROM deleted;  -- Note: Uses "deleted" table, not "inserted"
END
```

**Note:** For DELETE, use the `deleted` table instead of `inserted`!

---

### Trigger for Multiple Operations:

You can have one trigger respond to multiple events:

```sql
CREATE TRIGGER trg_log_all_changes
ON sales_employee
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
    -- Log insertions
    IF EXISTS (SELECT * FROM inserted) AND NOT EXISTS (SELECT * FROM deleted)
    BEGIN
        INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
        SELECT employee_id, 'Employee inserted', GETDATE()
        FROM inserted;
    END
    
    -- Log updates
    IF EXISTS (SELECT * FROM inserted) AND EXISTS (SELECT * FROM deleted)
    BEGIN
        INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
        SELECT employee_id, 'Employee updated', GETDATE()
        FROM inserted;
    END
    
    -- Log deletions
    IF NOT EXISTS (SELECT * FROM inserted) AND EXISTS (SELECT * FROM deleted)
    BEGIN
        INSERT INTO sales_employee_logs (employee_id, log_message, log_date)
        SELECT employee_id, 'Employee deleted', GETDATE()
        FROM deleted;
    END
END
```

---

## Advanced Log Message Examples

### More Detailed Messages:

```sql
-- Log with old and new salary comparison
'Salary updated from ' + CAST(d.salary AS VARCHAR) + ' to ' + CAST(i.salary AS VARCHAR)
FROM deleted d
JOIN inserted i ON d.employee_id = i.employee_id
```

### Log with User Information:

```sql
'Employee added by ' + SYSTEM_USER + ' at ' + CONVERT(VARCHAR, GETDATE(), 120)
```

---

## Real-World Benefits

### Analysis Possibilities:

```sql
-- How many employees were added today?
SELECT COUNT(*) 
FROM sales_employee_logs 
WHERE log_message LIKE 'New employee%' 
  AND log_date >= CAST(GETDATE() AS DATE);

-- Who was the last employee added?
SELECT TOP 1 * 
FROM sales_employee_logs 
ORDER BY log_date DESC;

-- All changes in the last week
SELECT * 
FROM sales_employee_logs 
WHERE log_date >= DATEADD(day, -7, GETDATE());
```

---

## Key Concepts Summary

| Concept | Meaning |
|---------|---------|
| **Audit Log** | Record of all changes to data |
| **Trigger** | Automatic action when event occurs |
| **inserted table** | Virtual table with NEW data |
| **deleted table** | Virtual table with OLD data |
| **AFTER** | Trigger runs after the action completes |
| **GETDATE()** | Current date and time |
| **IDENTITY** | Auto-incrementing number |

---

## The Power of Triggers

**What we achieved:**
- Automatic logging without manual work
- Complete audit trail of all changes
- Historical tracking for compliance
- Investigation capability when needed
- Security and accountability

**Set it once, it works forever!**

---

## Important Points to Remember

1. **Triggers run automatically** - no EXECUTE needed
2. **Use `inserted` table** for INSERT and UPDATE operations
3. **Use `deleted` table** for DELETE operations
4. **CAST numbers to VARCHAR** when building messages
5. **Use GETDATE()** to capture current timestamp
6. **Name triggers clearly** (e.g., trg_after_insert_employee)
7. **One table can have multiple triggers** for different events

---

## Practice Exercise

Create a trigger to log student grade updates:

```sql
-- Create log table
CREATE TABLE student_grade_logs (
    log_id INT IDENTITY PRIMARY KEY,
    student_id INT,
    old_grade INT,
    new_grade INT,
    change_date DATETIME
);

-- Create trigger
CREATE TRIGGER trg_log_grade_changes
ON student_grades
AFTER UPDATE
AS
BEGIN
    INSERT INTO student_grade_logs (student_id, old_grade, new_grade, change_date)
    SELECT 
        i.student_id,
        d.grade,
        i.grade,
        GETDATE()
    FROM inserted i
    JOIN deleted d ON i.student_id = d.student_id;
END
```

This trigger logs both the old and new grades whenever a student's grade is updated!

---

**Triggers are powerful tools that make your database smart and self-maintaining!**
