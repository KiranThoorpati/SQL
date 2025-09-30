# How to Create and Use Stored Procedures - Step by Step 📚

## Basic Syntax (The Rules)

### Part 1: Creating a Stored Procedure

```sql
CREATE PROCEDURE procedure_name
AS
BEGIN
    -- Your SQL commands go here
    -- You can write SELECT, INSERT, UPDATE, etc.
END
```

**Important Parts:**
- `CREATE PROCEDURE` = Tell SQL you're making a new stored procedure
- `procedure_name` = Give it a name (like naming your pet!)
- `AS` = Means "here's what it does"
- `BEGIN` and `END` = Like opening and closing brackets { }
- Everything between BEGIN and END = Your SQL commands

> - Basic Syntax (Rules) లో Stored Procedure create చేయడం ఇలా ఉంటుంది:

> - `CREATE PROCEDURE procedure_name AS BEGIN -- SQL commands END`

> - ఇక్కడ ముఖ్యమైన భాగాలు:

> - * `CREATE PROCEDURE` = SQL కి కొత్త stored procedure create చేస్తానని చెప్పటం
> - * `procedure_name` = దానికి పేరు ఇవ్వడం (మీ pet కి పేరు పెట్టినట్టు!)
> - * `AS` = “ఇది చేస్తుంది” అన్న అర్థం
> - * `BEGIN` మరియు `END` = code opening & closing brackets లా
> - * `BEGIN` మరియు `END` మధ్య వున్నది = నీ SQL commands (SELECT, INSERT, UPDATE లాంటి)

> - “AS” అంటే Stored Procedure లో “ఇక్కడటి code ఈ పని చేస్తుంది” అని SQL కి చెబుతూ ఉండటం. అర్థం సింపుల్‌గా, “ఇది Stored Procedure ఏ పని చేస్తుందో చెప్పే భాగం” అని గుర్తించుకో.

### Part 2: Running (Executing) a Stored Procedure

```sql
EXECUTE procedure_name
```

That's it! Just write EXECUTE and the name. Super simple! 🎉

> - Part 2: Stored Procedure run చేయడం అంటే, ఒక్క `EXECUTE procedure_name` అని రాయడం. అంతే! అద్భుతంగా సింపుల్ 🎉

---

## Real Example: Step-by-Step Tutorial

### 📋 **The Task:**
Create a weekly report that shows:
- Total number of US customers
- Average score of US customers

---

### **STEP 1: Write a Normal Query**

First, let's write a regular SQL query:

```sql
SELECT COUNT(*) AS Total_Customers,
       AVG(score) AS Average_Score
FROM sales_customers
WHERE country = 'USA'
```

**What this does:**
- Counts all customers from USA
- Calculates average score
- Shows the results

**Result:** You get a nice report with total customers and average score!

---

### **The Problem with Normal Queries** 😓

If you need this report every week:
- Option 1: Retype the whole query every time ❌
- Option 2: Save it in a text file, copy and paste it each time ❌

**Both options are annoying!** There's a better way...

---

### **STEP 2: Turn the Query into a Stored Procedure** ✨

Let's create a stored procedure with our query:

```sql
CREATE PROCEDURE Get_Customer_Summary
AS
BEGIN
    SELECT COUNT(*) AS Total_Customers,
           AVG(score) AS Average_Score
    FROM sales_customers
    WHERE country = 'USA'
END
```

**Breaking it down:**
1. `CREATE PROCEDURE` - We're making a new stored procedure
2. `Get_Customer_Summary` - This is the name we chose
3. `AS BEGIN...END` - Everything inside will run together
4. The SELECT query goes in the middle

**Run this code once** and your stored procedure is saved in the database! 🎊

---

### **Where is My Stored Procedure?**

You can find it in the database:
1. Open **Object Explorer**
2. Go to your database (example: sales_DB)
3. Look for **Programmability** folder
4. Inside, find **Stored Procedures** folder
5. There it is! Your `Get_Customer_Summary` is saved! 📁

> - నా Stored Procedure ఎక్కడుందంటే, database లో ఉంటుంది: Object Explorer ఓపెన్ చేయి, నీ database (ఉదా: sales_DB) కి వెళ్ళి, Programmability folder చూడాలి. అందులో Stored Procedures folder లో నీ Get_Customer_Summary saved గా ఉంటుంది! 📁

---

### **STEP 3: Use Your Stored Procedure** 🚀

Now whenever you need the report, just type:

```sql
EXECUTE Get_Customer_Summary
```

**That's it!** You get the same results without writing the whole query again!

---

## The Big Advantage 🌟

### **Before (Without Stored Procedure):**
```sql
-- You have to type/paste this EVERY TIME:
SELECT COUNT(*) AS Total_Customers,
       AVG(score) AS Average_Score
FROM sales_customers
WHERE country = 'USA'
```

### **After (With Stored Procedure):**
```sql
-- Just type this simple line:
EXECUTE Get_Customer_Summary
```

**You saved so much time and effort!** 🎯

---

## Quick Summary 📝

| Step | What to Do | Why |
|------|-----------|-----|
| 1️⃣ | Write your SQL query | Know what you want to do |
| 2️⃣ | Wrap it in CREATE PROCEDURE | Save it in the database |
| 3️⃣ | Use EXECUTE to run it | Get results anytime, easily! |

---

## Important Points to Remember 💡

1. **Write once, use forever** - Create the procedure once, execute it anytime
2. **No copy-paste needed** - No need to save queries in text files
3. **Stored in database** - Find it in Programmability → Stored Procedures
4. **Simple execution** - Just type EXECUTE and the name
5. **Perfect for repeated tasks** - Weekly reports, daily checks, etc.

---

## Practice Example 🎓

**Try this yourself:**

1. Think of a query you use often (like finding all students in your class)
2. Wrap it in CREATE PROCEDURE with a name
3. Run it to create the stored procedure
4. Use EXECUTE to call it anytime!

**It's like having a magic shortcut button!** ✨🔘
