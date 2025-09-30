# Error Handling in Stored Procedures - Plan B! 🛡️

## What is Error Handling?

**Simple Definition:** Error handling is creating a backup plan for when something goes wrong in your code.

**Think of it like:** Having a spare tire in your car! 🚗
- **Normal situation:** Drive smoothly
- **Problem occurs:** Tire gets flat
- **Backup plan:** Use the spare tire and keep going!

---

## Why Do We Need Error Handling?

### Without Error Handling:
- Program crashes and stops
- Shows confusing error messages
- You don't know what went wrong
- Can't control what happens next

### With Error Handling:
- Program continues or stops gracefully
- Shows clear, custom messages
- You know exactly what went wrong
- Full control over next steps (logging, cleanup, etc.)

**It's essential for professional programming!**

---

## TRY-CATCH Syntax (The Rules)

### Basic Structure:

```sql
BEGIN TRY
    -- Your code goes here
    -- SQL will TRY to execute this
END TRY
BEGIN CATCH
    -- Backup plan goes here
    -- SQL runs this if there's an ERROR
END CATCH
```

**Breaking it down:**
- `BEGIN TRY...END TRY` = Try to run this code
- `BEGIN CATCH...END CATCH` = If error happens, do this instead

---

## How TRY-CATCH Works - Simple Flow

### The Workflow:

```
START
  ↓
Execute TRY block
  ↓
Any errors?
  ↓
├─ NO ─────→ End (Skip CATCH completely)
│
└─ YES ────→ Jump to CATCH block
              ↓
           Execute CATCH
              ↓
             End
```

**Key Point:** CATCH only runs when there's an error!

---

## Example: Introducing an Error

Let's create an error on purpose to see how error handling works:

### The Problem Code:

```sql
SELECT COUNT(*) / 0  -- Dividing by zero = ERROR!
FROM sales_customers
WHERE country = @country;
```

**What happens:** SQL crashes with error message!

---

## Adding Error Handling to Our Stored Procedure

### Complete Example with TRY-CATCH:

```sql
ALTER PROCEDURE Get_Customer_Summary
    @country VARCHAR(50) = 'USA'
AS
BEGIN
    -- Wrap EVERYTHING in TRY-CATCH
    BEGIN TRY
        -- Declare variables
        DECLARE @total_customers INT,
                @average_score FLOAT;
        
        -- Part 1: Clean data
        IF EXISTS (
            SELECT 1
            FROM sales_customers
            WHERE score IS NULL AND country = @country
        )
        BEGIN
            PRINT 'Updating null scores to zero';
            UPDATE sales_customers
            SET score = 0
            WHERE score IS NULL AND country = @country;
        END
        ELSE
        BEGIN
            PRINT 'No null scores found';
        END
        
        -- Part 2: Generate reports (with intentional error)
        SELECT @total_customers = COUNT(*) / 0,  -- ERROR HERE!
               @average_score = AVG(score)
        FROM sales_customers
        WHERE country = @country;
        
        PRINT 'Total customers from ' + @country + ' is equal to ' + 
              CAST(@total_customers AS NVARCHAR);
        
    END TRY
    
    BEGIN CATCH
        -- Custom error handling starts here!
        PRINT 'An error occurred!';
        
        PRINT 'Error Message: ' + ERROR_MESSAGE();
        
        PRINT 'Error Number: ' + CAST(ERROR_NUMBER() AS NVARCHAR);
        
        PRINT 'Error Line: ' + CAST(ERROR_LINE() AS NVARCHAR);
        
        PRINT 'Error Procedure: ' + ERROR_PROCEDURE();
        
    END CATCH
END
```

---

## Error Functions - Getting Details

SQL provides special functions to get error information:

| Function | What It Returns | Example Output |
|----------|----------------|----------------|
| `ERROR_MESSAGE()` | Description of the error | "Divide by zero error" |
| `ERROR_NUMBER()` | Error code number | 8134 |
| `ERROR_LINE()` | Line number where error occurred | 25 |
| `ERROR_PROCEDURE()` | Name of the stored procedure | "Get_Customer_Summary" |
| `ERROR_SEVERITY()` | How serious the error is | 16 |
| `ERROR_STATE()` | Error state code | 1 |

---

## Building a Custom Error Message

### Step-by-Step Message Construction:

```sql
BEGIN CATCH
    -- Message 1: Alert
    PRINT 'An error occurred!';
    
    -- Message 2: Error description
    PRINT 'Error Message: ' + ERROR_MESSAGE();
    
    -- Message 3: Error number (must CAST to text)
    PRINT 'Error Number: ' + CAST(ERROR_NUMBER() AS NVARCHAR);
    
    -- Message 4: Line number (must CAST to text)
    PRINT 'Error Line: ' + CAST(ERROR_LINE() AS NVARCHAR);
    
    -- Message 5: Procedure name (already text)
    PRINT 'Error Procedure: ' + ERROR_PROCEDURE();
END CATCH
```

**Remember:** Numbers must be CAST to NVARCHAR for PRINT!

---

## What Happens When You Execute?

### Without Error Handling (Default SQL):

```sql
EXECUTE Get_Customer_Summary;
```

**Output:**
```
Msg 8134, Level 16, State 1
Divide by zero error encountered.
```
- Shows confusing SQL error
- Program stops immediately
- No control over what happens

---

### With Error Handling (Our Custom Code):

```sql
EXECUTE Get_Customer_Summary;
```

**Output in Messages Tab:**
```
An error occurred!
Error Message: Divide by zero error encountered
Error Number: 8134
Error Line: 25
Error Procedure: Get_Customer_Summary
```

- Shows clear, organized information
- You control what displays
- Professional and helpful
- No scary error messages

**No results in Results tab** - program caught the error gracefully!

---

## What Can You Do in CATCH Block?

The CATCH block is your control center! You can:

### 1. Display Custom Messages
```sql
PRINT 'Oops! Something went wrong. Please contact support.';
```

### 2. Log Errors to a Table
```sql
INSERT INTO error_log (error_date, error_message, procedure_name)
VALUES (GETDATE(), ERROR_MESSAGE(), ERROR_PROCEDURE());
```

### 3. Send Email Alerts
```sql
-- Use SQL Server's email feature
EXEC msdb.dbo.sp_send_dbmail
    @recipients = 'admin@company.com',
    @subject = 'Database Error Alert',
    @body = 'Error in stored procedure!';
```

### 4. Rollback Changes
```sql
-- Undo any changes made before the error
ROLLBACK TRANSACTION;
```

### 5. Delete Temporary Data
```sql
DELETE FROM temp_table WHERE process_id = @current_process;
```

### 6. Set Error Flags
```sql
UPDATE process_status 
SET status = 'FAILED', 
    error_time = GETDATE()
WHERE process_id = @id;
```

---

## Real-World Example: Student Grade Entry

```sql
CREATE PROCEDURE Add_Student_Grade
    @student_name VARCHAR(50),
    @marks INT
AS
BEGIN
    BEGIN TRY
        -- Validate marks (should be 0-100)
        IF @marks < 0 OR @marks > 100
        BEGIN
            -- Force an error
            RAISERROR('Marks must be between 0 and 100', 16, 1);
        END
        
        -- Insert the grade
        INSERT INTO student_grades (student_name, marks, date_entered)
        VALUES (@student_name, @marks, GETDATE());
        
        PRINT 'Grade added successfully for ' + @student_name;
        
    END TRY
    BEGIN CATCH
        -- Handle the error
        PRINT 'ERROR: Could not add grade!';
        PRINT 'Reason: ' + ERROR_MESSAGE();
        PRINT 'Student: ' + @student_name;
        
        -- Log to error table
        INSERT INTO grade_errors (student_name, error_msg, error_date)
        VALUES (@student_name, ERROR_MESSAGE(), GETDATE());
    END CATCH
END
```

---

## Where to Place TRY-CATCH?

### Option 1: Wrap Everything (Recommended)

```sql
CREATE PROCEDURE My_Procedure
AS
BEGIN
    BEGIN TRY
        -- ALL your code here
        -- Variable declarations
        -- Data cleaning
        -- Queries
        -- Updates
    END TRY
    BEGIN CATCH
        -- Error handling
    END CATCH
END
```

### Option 2: Wrap Specific Sections

```sql
CREATE PROCEDURE My_Procedure
AS
BEGIN
    -- Safe code here
    
    BEGIN TRY
        -- Only risky code here
    END TRY
    BEGIN CATCH
        -- Handle errors
    END CATCH
    
    -- More code here
END
```

---

## Important Points About TRY-CATCH

### What TRY-CATCH Can Catch:
- Division by zero
- Data type conversion errors
- Constraint violations (duplicate keys, etc.)
- NULL reference errors
- Syntax errors in dynamic SQL

### What TRY-CATCH Cannot Catch:
- Compile-time syntax errors
- Severe system errors
- Connection issues

---

## Comparison: Before and After

| Aspect | Without Error Handling | With Error Handling |
|--------|----------------------|-------------------|
| **Error Message** | Confusing SQL error | Clear custom message |
| **Control** | None - program crashes | Full control over response |
| **User Experience** | Scary and unclear | Professional and helpful |
| **Debugging** | Hard to find problem | Detailed error info |
| **Logging** | No automatic logging | Can log to table/file |
| **Recovery** | Program stops | Can cleanup and continue |

---

## Key Takeaways

| Concept | Meaning | Example |
|---------|---------|---------|
| **TRY** | Attempt to run code | `BEGIN TRY...END TRY` |
| **CATCH** | Backup plan if error | `BEGIN CATCH...END CATCH` |
| **ERROR_MESSAGE()** | Get error description | "Divide by zero" |
| **ERROR_NUMBER()** | Get error code | 8134 |
| **ERROR_LINE()** | Get line number | 25 |
| **ERROR_PROCEDURE()** | Get procedure name | "My_Procedure" |

---

## Practice Exercise

**Challenge:** Create a stored procedure that safely adds a new customer:

```sql
CREATE PROCEDURE Add_New_Customer
    @name VARCHAR(50),
    @email VARCHAR(100),
    @country VARCHAR(50)
AS
BEGIN
    BEGIN TRY
        -- Try to insert customer
        INSERT INTO customers (name, email, country)
        VALUES (@name, @email, @country);
        
        PRINT 'Customer added successfully: ' + @name;
        
    END TRY
    BEGIN CATCH
        -- Handle error (maybe duplicate email)
        PRINT 'ERROR: Could not add customer!';
        PRINT 'Name: ' + @name;
        PRINT 'Problem: ' + ERROR_MESSAGE();
        
    END CATCH
END
```

---

## Important Rules to Remember

1. **Always wrap risky code** in TRY-CATCH
2. **CATCH only runs** if there's an error
3. **Use error functions** to get details
4. **CAST numbers** to NVARCHAR in messages
5. **Create helpful messages** for users
6. **Log errors** for debugging later
7. **Test error handling** with intentional errors

**Remember:** Error handling is like wearing a seatbelt - you hope you never need it, but you're glad it's there when problems happen!
