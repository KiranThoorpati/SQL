# SQL Triggers - Automatic Actions! ⚡

## What is a Trigger?

**Simple Definition:** A trigger is a special stored procedure that runs **automatically** when something happens to a table (like inserting, updating, or deleting data).

**Key Difference from Stored Procedures:**
- **Stored Procedure:** You must run it manually with EXECUTE
- **Trigger:** Runs automatically when an event happens

**Think of it like:** A burglar alarm in your house
- When someone opens the door (EVENT) → Alarm rings automatically (TRIGGER)
- You don't press a button to sound the alarm - it happens automatically!

---

## How Triggers Work

### The Basic Concept:

```
TABLE
  ↓
Something happens (INSERT, UPDATE, DELETE)
  ↓
EVENT detected!
  ↓
TRIGGER fires automatically
  ↓
Additional action happens (log entry, validation, alert, etc.)
```

---

## What Events Can Trigger Actions?

Events are things that happen to a table:

| Event | What It Means | Example |
|-------|---------------|---------|
| **INSERT** | New data added | Adding a new student |
| **UPDATE** | Existing data changed | Changing a grade |
| **DELETE** | Data removed | Removing a student |

---

## Types of Triggers

### 1. DML Triggers (Data Manipulation Language)

**Respond to:** INSERT, UPDATE, DELETE operations

**Use cases:**
- Log changes to data
- Validate data before saving
- Update related tables automatically
- Prevent unauthorized deletions
- Send notifications

**This is what we'll focus on!**

---

### 2. DDL Triggers (Data Definition Language)

**Respond to:** Schema changes like CREATE, ALTER, DROP

**Examples:**
- Someone creates a new table
- Someone drops a view
- Database structure changes

**Use cases:**
- Track database structure changes
- Prevent accidental table deletions
- Audit who changed what

---

### 3. Logon Triggers

**Respond to:** User login events

**Use cases:**
- Track who logs in and when
- Restrict login times
- Security monitoring

---

## Two Types of DML Triggers

### AFTER Triggers (Execute AFTER the event)

```
User does INSERT
  ↓
Data is inserted into table ✓
  ↓
AFTER trigger fires
  ↓
Additional actions happen
```

**Think of it like:** Taking a photo AFTER someone scores a goal
- Goal happens first
- Then photo is taken

**Use cases:**
- Logging changes (audit trail)
- Updating summary tables
- Sending notifications

---

### INSTEAD OF Triggers (Execute DURING the event)

```
User does INSERT
  ↓
INSTEAD OF trigger fires (interrupts)
  ↓
Trigger decides what to do
  ↓
May or may not actually insert data
```

**Think of it like:** A security guard at a door
- Person tries to enter
- Guard checks first (during the attempt)
- Guard decides: allow or deny

**Use cases:**
- Data validation before allowing changes
- Preventing certain operations
- Complex business rules
- Redirecting operations to different tables

---

## AFTER vs INSTEAD OF - Visual Comparison

### AFTER Trigger Timeline:
```
1. User runs: INSERT INTO students VALUES ('John', 85)
2. ✓ John is added to the table
3. TRIGGER runs: Log this change
4. ✓ Log entry created
```

The INSERT happened, then the trigger ran.

---

### INSTEAD OF Trigger Timeline:
```
1. User runs: INSERT INTO students VALUES ('John', 85)
2. ⚠️ TRIGGER intercepts BEFORE insert happens
3. TRIGGER checks: Is grade valid (0-100)?
4. Decision:
   - If YES → Allow the insert
   - If NO → Reject and show error
```

The trigger decides whether the INSERT should happen at all.

---

## Real-World Examples

### Example 1: AFTER Trigger - Audit Log

**Scenario:** Track who deletes students from the database

```sql
CREATE TRIGGER Log_Student_Deletion
ON students
AFTER DELETE
AS
BEGIN
    -- Automatically log the deletion
    INSERT INTO audit_log (action, table_name, deleted_by, deleted_date)
    VALUES ('DELETE', 'students', CURRENT_USER, GETDATE());
END
```

**What happens:**
1. Teacher deletes a student
2. Student is removed from table
3. Trigger automatically logs this action
4. Audit trail is maintained

---

### Example 2: INSTEAD OF Trigger - Validation

**Scenario:** Don't allow grades above 100

```sql
CREATE TRIGGER Validate_Grade
ON student_grades
INSTEAD OF INSERT
AS
BEGIN
    -- Check if grade is valid
    IF EXISTS (SELECT * FROM inserted WHERE grade > 100)
    BEGIN
        PRINT 'ERROR: Grade cannot be more than 100!';
        -- Don't insert the data
    END
    ELSE
    BEGIN
        -- Grade is valid, allow the insert
        INSERT INTO student_grades
        SELECT * FROM inserted;
    END
END
```

**What happens:**
1. Someone tries to insert grade = 150
2. Trigger intercepts BEFORE insert
3. Trigger checks: 150 > 100? YES
4. Trigger blocks the insert and shows error
5. Invalid data never enters the table

---

## Where Triggers are "Attached"

Triggers are attached to specific tables:

```
Table: students
  ├── Trigger 1: Log all insertions
  ├── Trigger 2: Validate grades
  └── Trigger 3: Update summary table

Table: orders
  ├── Trigger 1: Calculate total price
  └── Trigger 2: Update inventory
```

Each table can have multiple triggers!

---

## What Can Triggers Do?

Triggers can perform various actions:

### 1. Insert Data Elsewhere
```sql
-- When order is placed, add to order history
INSERT INTO order_history ...
```

### 2. Update Related Tables
```sql
-- When product is sold, reduce inventory count
UPDATE inventory SET quantity = quantity - 1 ...
```

### 3. Validate Data
```sql
-- Check if email format is valid
IF @email NOT LIKE '%@%.%'
    RAISERROR('Invalid email', 16, 1);
```

### 4. Send Notifications
```sql
-- Alert when inventory is low
IF @quantity < 10
    PRINT 'WARNING: Low inventory!';
```

### 5. Prevent Actions
```sql
-- Don't allow deletion of important records
IF @record_type = 'CRITICAL'
    RAISERROR('Cannot delete critical records', 16, 1);
```

### 6. Calculate Values
```sql
-- Automatically calculate order total
UPDATE orders SET total = quantity * price ...
```

---

## Trigger vs Stored Procedure

| Feature | Stored Procedure | Trigger |
|---------|-----------------|---------|
| **How it runs** | Manually with EXECUTE | Automatically on events |
| **When to use** | On-demand tasks | Automatic responses |
| **Can be called directly** | Yes | No |
| **Attached to** | Database | Specific table |
| **Parameters** | Can have parameters | No parameters (uses special tables) |

---

## Important Concepts

### Event
An action that happens to a table (INSERT, UPDATE, DELETE)

### Fire
When a trigger runs, we say it "fires"

### Attach
Connecting a trigger to a specific table

### Automatic
Triggers run without you telling them to - they respond to events

---

## Benefits of Using Triggers

### 1. Automation
- No need to remember to do tasks manually
- Actions happen consistently every time

### 2. Data Integrity
- Enforce business rules automatically
- Prevent invalid data from entering tables

### 3. Audit Trail
- Track all changes automatically
- Know who did what and when

### 4. Consistency
- Same validation rules apply to everyone
- No human errors or forgotten steps

### 5. Security
- Prevent unauthorized changes
- Add extra checks before allowing operations

---

## When to Use Each Type

### Use AFTER Triggers When:
- Logging changes for audit purposes
- Updating summary or related tables
- Sending notifications after confirmed changes
- You want the main action to complete first

### Use INSTEAD OF Triggers When:
- Validating data before allowing changes
- Implementing complex business rules
- Preventing certain operations
- You need to control whether the action happens at all

---

## Key Takeaways

| Concept | Meaning |
|---------|---------|
| **Trigger** | Special stored procedure that runs automatically |
| **Event** | Something that happens to a table (INSERT, UPDATE, DELETE) |
| **Fire** | When a trigger executes automatically |
| **DML Trigger** | Responds to data changes (INSERT, UPDATE, DELETE) |
| **AFTER** | Runs after the event completes |
| **INSTEAD OF** | Runs during the event, can prevent it |
| **Automatic** | No manual EXECUTE needed |

---

## Summary

Triggers are like automatic helpers that:
- Watch your tables for changes
- React immediately when something happens
- Perform additional actions automatically
- Help maintain data quality and consistency
- Create audit trails without extra work

**The main advantage:** Set them up once, and they work forever - automatically protecting and managing your data!

---

## Important Rules to Remember

1. **Triggers run automatically** - no EXECUTE needed
2. **Each trigger is attached to one table** - but a table can have many triggers
3. **AFTER triggers** run after the action completes
4. **INSTEAD OF triggers** can prevent the action from happening
5. **DML triggers** respond to INSERT, UPDATE, DELETE
6. **Use triggers wisely** - they add overhead to database operations

In the next lessons, you'll learn how to actually create and use these triggers with real examples!
