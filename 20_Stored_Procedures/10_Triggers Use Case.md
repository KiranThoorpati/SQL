# Creating Triggers - Audit Log Example 📋

## The Use Case: Maintaining Audit Logs

### What is an Audit Log?

**Simple Definition:** An audit log is a record that tracks all changes made to important data - who did what, when, and what changed.

**Think of it like:** A school attendance register that tracks:
- When students arrive
- When they leave
- Who marked them present
- Any changes to their records

> - ఆడిట్ లాగ్ అంటే ఏమిటి అంటే — అది ఒక రికార్డ్ లాంటిది, దానిలో ఎవరు ఎప్పుడు ఏమి మార్చారో, ఏ డేటాలో మార్పు జరిగిందో అన్నది నమోదు చేస్తారు. దీన్ని స్కూల్ హాజరు రిజిస్టర్‌లా ఊహించుకో — విద్యార్థులు ఎప్పుడు వచ్చారు, ఎప్పుడు వెళ్లారు, ఎవరు హాజరు పెట్టారు, ఎవరైనా హాజరు రికార్డులో ఏమైనా మార్పులు చేశారా అన్నదాన్ని గుర్తుంచుకునే విధంగా ఉంటుంది.

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

> - మనకు ఆడిట్ లాగ్స్ ఎందుకు అవసరం అంటే — ఉదాహరణకి ఉద్యోగుల టేబుల్‌లో కొత్త ఉద్యోగులను జోడించడం, జీతం మార్పులు చేయడం, ఉద్యోగం నుంచి తీసేయడం, లేదా పోస్టు మార్చడం లాంటివి ఉంటాయి. ఇవి చాలా సున్నితమైన వివరాలు కాబట్టి, ఎప్పుడు మార్పు జరిగింది, ఎవరు చేశారు, ఏమి మార్చారు అన్నది ట్రాక్‌ చేయాలి. ఇలా చేయడం వల్ల నిబంధనలకు అనుగుణంగా పని చేస్తున్నామో లేదో చూడొచ్చు, ఎక్కడైనా సమస్య వస్తే దాని మూలం కనుక్కోవచ్చు, భద్రత మరియు బాధ్యత స్పష్టంగా ఉంటుంది, అలాగే పాత మార్పుల చరిత్ర కూడా దొరుకుతుంది.

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
> - మొదటగా, అన్ని లాగ్ వివరాలను సేవ్ చేసేందుకు ఒక టేబుల్‌ను సృష్టించాలి. ఉదాహరణకు:

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

```
ఈ టేబుల్‌లోని కాలమ్స్ అర్థం ఇలా —
log_id: ప్రతి లాగ్‌కు ప్రత్యేక ID (ఆటోమేటిక్‌గా పెరుగుతుంది),
employee_id: ఏ ఉద్యోగి రికార్డ్‌లో మార్పు జరిగిందో,
log_message: ఏమి జరిగిందో చిన్న వివరణ,
log_date: ఆ మార్పు ఎప్పుడు జరిగిందో నమోదు చేసేందుకు ఉపయోగిస్తారు.
```

---

### Step 2: Create the Trigger

Now create a trigger that logs every new employee insertion:
> - ఇప్పుడు, ప్రతి సారి కొత్త ఉద్యోగి రికార్డ్ టేబుల్‌లో చేర్చబడినప్పుడు ఆ వివరాన్ని లాగ్ టేబుల్‌లో ఆటోమేటిక్‌గా నమోదు చేసే ట్రిగ్గర్‌ని సృష్టించాలి.

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

> - trg_after_insert_employee అనే పేరు ఉన్న ట్రిగ్గర్ (ఇందులో “trg_” అంటే ఇది ట్రిగ్గర్ అని సూచిస్తుంది), ఇది sales_employee టేబుల్‌కి జత చేయబడింది, మరియు ఎవరైనా కొత్త రికార్డ్ ఇన్‌సర్ట్ చేసిన తర్వాత ఆటోమేటిక్‌గా పనిచేస్తుంది.

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

> - **“inserted” టేబుల్** అంటే — ఇది ట్రిగ్గర్ పనిచేసే సమయంలో తాత్కాలికంగా ఉండే ఒక వర్చువల్ టేబుల్‌. ఈ టేబుల్‌లో ఇప్పుడే ఇన్‌సర్ట్ చేసిన కొత్త డేటా ఉంటుంది, మరియు ఇది మీ మెయిన్ టేబుల్‌లో ఉన్న కాలమ్స్‌లాగే ఉంటుంది. ఇది SQL ఆటోమేటిక్‌గా సృష్టిస్తుంది, కానీ ట్రిగ్గర్ బయట దీన్ని యాక్సెస్ చేయలేము ఎందుకంటే ఇది తాత్కాలికంగా మాత్రమే ఉంటుంది.

> - ఇలాంటి స్పెషల్ టేబుల్స్ రెండూ ఉంటాయి —
> - **inserted** → కొత్త డేటా (INSERT, UPDATE సమయంలో)
> - **deleted** → పాత డేటా (DELETE, UPDATE సమయంలో)

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

> - ఇక్కడ `'New employee added - Employee ID: ' + CAST(employee_id AS VARCHAR)` అని రాయడం వల్ల, మెసేజ్ తయారవుతుంది. కానీ **CAST** ఎందుకు వాడుతున్నామంటే — `employee_id` ఒక **INTEGER** (అంటే నంబర్), కానీ మెసేజ్ మాత్రం **STRING** (అక్షరాల రూపంలో ఉన్న టెక్స్ట్) కావాలి. అందుకే **CAST** వాడి నంబర్‌ను టెక్స్ట్‌గా మార్చుతాం. ఉదాహరణకి ఫలితం ఇలా వస్తుంది — `"New employee added - Employee ID: 6"`

---

### Using GETDATE():

```sql
log_date = GETDATE()
```

`GETDATE()` returns the current date and time automatically.

> - `GETDATE()` అంటే ప్రస్తుత తేదీ మరియు సమయాన్ని ఆటోమేటిక్‌గా ఇస్తుంది. ఉదాహరణకి, `log_date = GETDATE()` అని రాస్తే, ఆ రికార్డ్ సృష్టించిన సమయం (date & time) టేబుల్‌లో ఆటోమేటిక్‌గా సేవ్ అవుతుంది.

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

> - కొత్త ఉద్యోగిని చేర్చడం ఇలా జరుగుతుంది:

```sql
INSERT INTO sales_employee (employee_id, first_name, last_name, position, birth_date, gender, salary, hierarchy)
VALUES (6, 'Maria', 'Garcia', 'HR', '1990-05-15', 'Female', 55000, 3);
```

**What happens automatically:**
1. Maria is inserted into `sales_employee` table
2. Trigger fires immediately
3. Log entry is created in `sales_employee_logs`

> - ఇక్కడ ఏమి అవుతుంది అంటే — Maria sales_employee టేబుల్‌లో చేర్చబడుతుంది, వెంటనే ట్రిగ్గర్ పనిచేస్తుంది, మరియు ఆచరణలో ఉన్న లాగ్ sales_employee_logs టేబుల్‌లో ఆటోమేటిక్‌గా నమోదు అవుతుంది.

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
> - వచ్చింది! లాగ్ ఆటోమేటిక్‌గా క్రియేట్ అయ్యింది!

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

> - మీరు కొత్త ఉద్యోగిని చేర్చితే, ఆ మార్పు గురించి sales_employee_logs అనే టేబుల్‌లో ఏం మారింది, ఎవరు చేర్చారు, ఎప్పుడు చేర్చారు అన్న సమాచారం ఆటోమేటిక్‌గా రికార్డ్ అవుతుంది.

---

## Finding Your Triggers

### In SQL Server Management Studio:

1. Go to **Object Explorer**
2. Expand your database (e.g., `sales_DB`)
3. Expand **Tables**
4. Expand your table (e.g., `sales_employee`)
5. Expand **Triggers** folder
6. You'll see: `trg_after_insert_employee`

> - మీ ట్రిగ్గర్‌లు ఎలా చూడాలో SQL Server Management Studio లో ఇలా చెయ్యాలి: Object Explorer లోకి వెళ్లి, మీ డేటాబేస్ (ఉదాహరణకి **sales_DB**)ని ఎక్స్‌పాండ్ చేయండి, ఆ తర్వాత Tables ని ఎక్స్‌పాండ్ చేసి, మీ టేబుల్ (ఉదాహరణకి **sales_employee**)ని ఎక్స్‌పాండ్ చేయండి, Triggers ఫోల్డర్‌ని ఎక్స్‌పాండ్ చేయండి. ఇక్కడ మీరు **trg_after_insert_employee** ట్రిగ్గర్‌ని చూడవచ్చు.

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
> - గమనిక: **DELETE** క్వెరీ కోసం, **inserted** బదులు **deleted** టేబుల్‌ను ఉపయోగించాలి.

---

### Trigger for Multiple Operations:

You can have one trigger respond to multiple events:
> - ఒకే ట్రిగ్గర్‌ను ఒకటి కంటే ఎక్కువ ఈవెంట్స్‌కు రెస్పాండ్ చేయించవచ్చు.

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
> - అంటే, ఎవరు కొత్త ఉద్యోగిని చేర్చారో మరియు ఎప్పుడు చేర్చారో స్పష్టంగా లాగ్‌లో రికార్డు అవుతుంది.
> - CONVERT(VARCHAR, ..., 120): DateTime ను Text (VARCHAR) లో మార్చుతుంది

---

## Real-World Benefits

### [Analysis Possibilities:](./10/1_New_emp_added_today.md)

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
