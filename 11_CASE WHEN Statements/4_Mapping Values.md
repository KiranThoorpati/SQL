# SQL CASE Statements: Mapping Values
## A Simple Guide for 7th Grade Students

## What is Value Mapping?

**Mapping** means converting data from one form to another form to make it easier to read and understand.

**Real-Life Example:**
- Your school stores grades as numbers: 1, 2, 3, 4
- But in reports, you want to see: "Excellent", "Good", "Fair", "Poor"
- Mapping helps you convert 1 → "Excellent", 2 → "Good", etc.

---

## Why Do We Need Value Mapping?

Sometimes databases store information as **codes** or **abbreviations** to save space and make the computer work faster:

| Stored in Database | What We Want to Show |
|-------------------|---------------------|
| M / F | Male / Female |
| 1 / 0 | Active / Inactive |
| USA / GER | United States / Germany |

**The Problem:** These codes are hard for humans to read in reports!

**The Solution:** Use CASE statements to translate codes into readable text!

---

## Example 1: Converting Gender Codes to Full Text

### The Original Data

Let's say our database stores gender as single letters:

```sql
SELECT 
    employee_id,
    first_name,
    last_name,
    gender
FROM sales_employees;
```

**Output:**
```
employee_id | first_name | last_name | gender
------------|------------|-----------|--------
1           | Sarah      | Johnson   | F
2           | Mike       | Smith     | M
3           | Emma       | Brown     | F
4           | John       | Davis     | M
5           | Lisa       | Wilson    | F
```

---

### Mapping F and M to Full Text

Now let's convert F → Female and M → Male:

```sql
SELECT 
    employee_id,
    first_name,
    last_name,
    gender,
    CASE
        WHEN gender = 'F' THEN 'Female'
        WHEN gender = 'M' THEN 'Male'
        ELSE 'Not Available'
    END AS gender_full_text
FROM sales_employees;
```

**Output:**
```
employee_id | first_name | last_name | gender | gender_full_text
------------|------------|-----------|--------|------------------
1           | Sarah      | Johnson   | F      | Female
2           | Mike       | Smith     | M      | Male
3           | Emma       | Brown     | F      | Female
4           | John       | Davis     | M      | Male
5           | Lisa       | Wilson    | F      | Female
```

**How it works:**
- When gender = 'F', show "Female"
- When gender = 'M', show "Male"
- If there's any other value (or null), show "Not Available"

---

## Example 2: Converting Full Names to Abbreviations

Sometimes you need to go the **opposite direction** - from long text to short codes!

### Finding All Possible Values First

**Important Tip:** Before mapping, check what values exist in your column:

```sql
SELECT DISTINCT country
FROM sales_customers;
```

**Output:**
```
country
---------
Germany
USA
```

Now you know there are only 2 countries to map!

---

### The Original Data

```sql
SELECT 
    customer_id,
    first_name,
    last_name,
    country
FROM sales_customers;
```

**Output:**
```
customer_id | first_name | last_name | country
------------|------------|-----------|----------
1           | Anna       | Mueller   | Germany
2           | Tom        | Brown     | USA
3           | Hans       | Schmidt   | Germany
4           | Mary       | Jones     | USA
5           | Peter      | Wagner    | Germany
```

---

### Mapping to Abbreviations

```sql
SELECT 
    customer_id,
    first_name,
    last_name,
    country,
    CASE
        WHEN country = 'Germany' THEN 'DE'
        WHEN country = 'USA' THEN 'US'
        ELSE 'NA'
    END AS country_abbreviation
FROM sales_customers;
```

**Output:**
```
customer_id | first_name | last_name | country | country_abbreviation
------------|------------|-----------|---------|---------------------
1           | Anna       | Mueller   | Germany | DE
2           | Tom        | Brown     | USA     | US
3           | Hans       | Schmidt   | Germany | DE
4           | Mary       | Jones     | USA     | US
5           | Peter      | Wagner    | Germany | DE
```

---

## Special Trick: The Quick Format (Simple Form)

When you're mapping values from **ONE column only** and using **only equals (=)**, there's a shorter way!

### Regular Format (Full Form)

```sql
CASE
    WHEN country = 'Germany' THEN 'DE'
    WHEN country = 'USA' THEN 'US'
    ELSE 'NA'
END
```

### Quick Format (Short Form)

```sql
CASE country
    WHEN 'Germany' THEN 'DE'
    WHEN 'USA' THEN 'US'
    ELSE 'NA'
END
```

---

### Complete Example Using Quick Format

```sql
SELECT 
    customer_id,
    first_name,
    last_name,
    country,
    CASE country
        WHEN 'Germany' THEN 'DE'
        WHEN 'USA' THEN 'US'
        ELSE 'NA'
    END AS country_abbreviation
FROM sales_customers;
```

**Output:** (Same as before)
```
customer_id | first_name | last_name | country | country_abbreviation
------------|------------|-----------|---------|---------------------
1           | Anna       | Mueller   | Germany | DE
2           | Tom        | Brown     | USA     | US
3           | Hans       | Schmidt   | Germany | DE
4           | Mary       | Jones     | USA     | US
5           | Peter      | Wagner    | Germany | DE
```

---

## Quick Format vs. Full Format

| Feature | Full Format | Quick Format |
|---------|-------------|--------------|
| Can use multiple columns | ✅ Yes | ❌ No (only ONE column) |
| Can use >, <, >= | ✅ Yes | ❌ No (only = equals) |
| More flexible | ✅ Yes | ❌ No |
| Shorter to write | ❌ No | ✅ Yes |

**Recommendation:** Use the **full format** most of the time because it's more flexible!

---

## More Practice Examples

### Example 3: Order Status Codes

```sql
SELECT 
    order_id,
    status_code,
    CASE status_code
        WHEN 1 THEN 'Active'
        WHEN 0 THEN 'Inactive'
        ELSE 'Unknown'
    END AS status_description
FROM orders;
```

**Sample Output:**
```
order_id | status_code | status_description
---------|-------------|-------------------
101      | 1           | Active
102      | 0           | Inactive
103      | 1           | Active
104      | 0           | Inactive
```

---

### Example 4: Day of Week

```sql
SELECT 
    appointment_id,
    day_number,
    CASE day_number
        WHEN 1 THEN 'Monday'
        WHEN 2 THEN 'Tuesday'
        WHEN 3 THEN 'Wednesday'
        WHEN 4 THEN 'Thursday'
        WHEN 5 THEN 'Friday'
        WHEN 6 THEN 'Saturday'
        WHEN 7 THEN 'Sunday'
        ELSE 'Invalid Day'
    END AS day_name
FROM appointments;
```

**Sample Output:**
```
appointment_id | day_number | day_name
---------------|------------|----------
1              | 1          | Monday
2              | 5          | Friday
3              | 7          | Sunday
4              | 3          | Wednesday
```

---

## Important Tips for Mapping Values

1. **Always use DISTINCT first** to see all possible values:
   ```sql
   SELECT DISTINCT column_name FROM table_name;
   ```

2. **Be careful with spelling and capitalization** - 'USA' is different from 'usa'!

3. **Always include an ELSE** statement to handle unexpected values

4. **Use the quick format only when:**
   - You're checking ONE column only
   - You're only using equals (=)
   - You're doing simple mapping

---

## Summary

**Value Mapping** = Converting data from one form to another

**Common Uses:**
- Codes → Full descriptions (F → Female)
- Full text → Abbreviations (Germany → DE)
- Numbers → Descriptions (1 → Active)

**Remember:** Mapping makes your reports easier for humans to read! 📊✨
