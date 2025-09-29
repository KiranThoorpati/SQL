# Creating and Using Partitioned Tables in SQL

## What We've Built So Far

Remember our 4-step setup? We created:
1. **Partition Function** - Rules for splitting data
2. **File Groups** - Folders to organize files
3. **Data Files** - Physical files to store data  
4. **Partition Scheme** - Connections between everything

Now it's time to **USE** all this setup by creating a partitioned table!

---

## Creating a Partitioned Table

### Regular Table vs Partitioned Table

**Regular Table Creation:**
```sql
CREATE TABLE sales.orders (
    order_id INT,
    order_date DATE,
    sales INT
);
```

**Partitioned Table Creation:**
```sql
CREATE TABLE sales.orders_partitioned (
    order_id INT,
    order_date DATE,
    sales INT
) ON scheme_partition_by_year (order_date);
```

### The Magic Word: "ON"

The key difference is the **ON** clause at the end:
- `ON scheme_partition_by_year` - tells SQL to use our partition scheme
- `(order_date)` - tells SQL which column to use for partitioning

### Important Rules:
- **Column must match the function**: Since our partition function splits by DATE, we must specify a DATE column
- **Wrong column = Error**: Can't use `order_id` or `sales` because our function expects dates
- **Column must exist**: The column you specify must be one of the table's columns

---

## How Data Gets Stored

When you insert data into a partitioned table, SQL automatically decides where to put it based on your partition function rules.

### The Process:
1. **You insert data** with a specific date
2. **Partition Function** decides which partition it belongs to
3. **Partition Scheme** maps that partition to the correct file group
4. **Data gets stored** in the corresponding physical file

---

## Testing Our Partitioned Table

Let's test our setup by inserting data from different years:

### Test 1: 2023 Data
```sql
INSERT INTO sales.orders_partitioned 
VALUES (1, '2023-06-15', 100);
```

**Expected Result:** Data should go to Partition 1 (FG_2023)

### Test 2: 2024 Data  
```sql
INSERT INTO sales.orders_partitioned 
VALUES (2, '2024-08-20', 150);
```

**Expected Result:** Data should go to Partition 2 (FG_2024)

### Test 3: Boundary Test (Last day of 2025)
```sql
INSERT INTO sales.orders_partitioned 
VALUES (3, '2025-12-31', 200);
```

**Expected Result:** Since we used LEFT boundary, this should go to Partition 3 (FG_2025)

### Test 4: Future Date (2026)
```sql
INSERT INTO sales.orders_partitioned 
VALUES (4, '2026-01-01', 300);
```

**Expected Result:** Data should go to Partition 4 (FG_2026)

---

## Checking Where Data Is Stored

After inserting data, we can check which partition each row went to using system tables:

### Sample Query to Check Partitions:
```sql
SELECT 
    partition_number,
    rows_count,
    filegroup_name
FROM sys.partitions p
JOIN sys.allocation_units au ON p.partition_id = au.container_id
JOIN sys.filegroups fg ON au.data_space_id = fg.data_space_id
WHERE object_id = OBJECT_ID('sales.orders_partitioned');
```

### Expected Results After Our Tests:
| Partition | Rows | File Group | Data |
|-----------|------|------------|------|
| 1 | 1 | FG_2023 | 2023 order |
| 2 | 1 | FG_2024 | 2024 order |
| 3 | 1 | FG_2025 | 2025 order |
| 4 | 1 | FG_2026 | 2026 order |

---

## Understanding the Flow

Here's how everything works together when you insert data:

### Visual Flow:
```
INSERT data with date '2025-12-31'
            ↓
    Partition Function checks boundary
    (Since LEFT boundary: goes to Partition 3)
            ↓
    Partition Scheme maps Partition 3 to FG_2025
            ↓
    Data gets physically stored in FG_2025's data file
```

### Step-by-Step Example:
1. **Insert:** `'2025-12-31'` with sales data
2. **Function Check:** Is this date a boundary? Yes! (Last day of 2025)
3. **Left Rule Applied:** Boundary belongs to LEFT partition (Partition 3)
4. **Scheme Mapping:** Partition 3 → FG_2025 file group  
5. **Physical Storage:** Data saved in FG_2025's data file

---

## Why Boundary Testing Is Important

Testing boundary dates is crucial because:
- **Boundaries are tricky** - Easy to get wrong
- **Left vs Right matters** - Changes where data goes
- **Verify your logic** - Make sure data goes where you expect

### Common Boundary Mistakes:
- Thinking boundary goes to wrong partition
- Forgetting about LEFT/RIGHT setting
- Not testing edge cases

---

## Finding Partition Information in SQL Server

You can also view partition information through SQL Server Management Studio:

**Path:** Database → Storage → Partition Schemes and Partition Functions

This gives you a visual way to see:
- All your partition schemes
- All your partition functions  
- Quick overview without writing queries

---

## Complete Connection Summary

Here's how all the layers work together:

```
TABLE (orders_partitioned)
    ↓ connected to
PARTITION SCHEME (scheme_partition_by_year)
    ↓ uses
PARTITION FUNCTION (partition_by_year)
    ↓ creates partitions that map to
FILE GROUPS (FG_2023, FG_2024, FG_2025, FG_2026)
    ↓ contain
DATA FILES (P2023.ndf, P2024.ndf, P2025.ndf, P2026.ndf)
```

### When Data Is Inserted:
1. **Table** receives the INSERT command
2. **Partition Function** determines which partition based on date
3. **Partition Scheme** maps partition to correct file group
4. **File Group** directs data to its data file
5. **Data File** physically stores the row

---

## Key Points to Remember

### Success Indicators:
✅ **Data goes to expected partitions** based on dates
✅ **Boundary dates work correctly** according to LEFT/RIGHT rules  
✅ **Each partition has appropriate row counts**
✅ **System queries show proper distribution**

### Why This Is Exciting:
- **Control over data placement** - You decide where data goes
- **Understanding database internals** - See how databases really work
- **Performance benefits** - Queries only scan relevant partitions
- **Flexibility** - Can manage different data differently

### Testing Checklist:
- [ ] Test data from each expected year range
- [ ] Test boundary dates specifically  
- [ ] Verify data goes to correct partitions
- [ ] Check row counts in each partition
- [ ] Test edge cases (very old/new dates)

---

## Common Issues and Solutions

### Problem: Data not going to expected partition
**Solution:** Check your partition function boundaries and LEFT/RIGHT setting

### Problem: Error when creating partitioned table  
**Solution:** Verify column data type matches partition function data type

### Problem: Can't find partition information
**Solution:** Use system tables or SQL Server Management Studio storage section

### Problem: Boundaries behaving unexpectedly
**Solution:** Test with specific dates and verify LEFT vs RIGHT behavior

---

## What's Next?

Once your partitioned table is working:
1. **Query optimization** - Learn how SQL uses partitions for faster queries
2. **Partition maintenance** - Add/remove partitions as data grows
3. **Performance monitoring** - Track how partitioning improves performance
4. **Advanced features** - Partition switching, merging, splitting

**Remember:** You now have full control over where your data lives physically - that's powerful database management!
