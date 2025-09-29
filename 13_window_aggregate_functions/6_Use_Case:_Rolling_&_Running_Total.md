# SQL Running Total & Rolling Total - Easy Notes

## Why Are These Important?

These concepts help us:
- **Track things over time** (like monthly sales, savings, scores)
- **Compare progress** (are we reaching our goals?)
- **Understand trends** (is our business growing or shrinking?)

---

## What is a Running Total?

A **Running Total** keeps **adding up everything from the beginning** without forgetting anything!

### Real-Life Example:
Imagine you're saving money each month:
- January: You save ₹20 → Total savings = ₹20
- February: You save ₹10 → Total savings = ₹20 + ₹10 = ₹30
- March: You save ₹30 → Total savings = ₹30 + ₹30 = ₹60
- April: You save ₹5 → Total savings = ₹60 + ₹5 = ₹65

**See?** You never forget any previous money. You keep adding everything!

---

## What is a Rolling Total?

A **Rolling Total** only looks at a **fixed time window** (like last 3 months) and keeps moving forward.

### Real-Life Example:
You want to know your savings for the **last 3 months only**:

- **January:** ₹20 → (only 1 month) = ₹20
- **February:** ₹20, ₹10 → (only 2 months) = ₹30
- **March:** ₹20, ₹10, ₹30 → (3 months) = ₹60
- **April:** ₹10, ₹30, ₹5 → (3 months, dropped Jan!) = ₹45
- **May:** ₹30, ₹5, ₹70 → (3 months, dropped Feb!) = ₹105

**See?** When you add a new month, you **drop the oldest month**. The window keeps "rolling" or "sliding" forward!

---

## Key Differences

| Running Total | Rolling Total |
|---------------|---------------|
| Adds everything from start | Only looks at fixed window |
| Never forgets old data | Drops oldest when adding new |
| Keeps growing bigger | Stays same size |
| Like: Total savings ever | Like: Last 3 months savings |

---

## Visual Understanding

### Running Total (Growing Window):
```
Month 1: [20] = 20
Month 2: [20, 10] = 30
Month 3: [20, 10, 30] = 60
Month 4: [20, 10, 30, 5] = 65  ← Keeps growing!
Month 5: [20, 10, 30, 5, 70] = 135
```

### Rolling Total (Fixed Window of 3 months):
```
Month 1: [20] = 20
Month 2: [20, 10] = 30
Month 3: [20, 10, 30] = 60
Month 4: [10, 30, 5] = 45      ← Dropped 20!
Month 5: [30, 5, 70] = 105     ← Dropped 10!
```

---

## How to Write in SQL

### Running Total (Easy!)
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER (ORDER BY month) AS running_total
FROM sales_data;
```

**What it does:**
- Sorts data by month
- Adds up all sales from beginning to current month
- Never drops any data

### Rolling Total (Need to specify window!)
```sql
SELECT 
    month,
    sales,
    SUM(sales) OVER (
        ORDER BY month 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS rolling_total_3months
FROM sales_data;
```

**What it does:**
- Sorts data by month
- Only looks at **3 months** (2 previous + current)
- Drops oldest month when adding new one

---

## Example with Data

**Sales Data:**

| Month | Sales |
|-------|-------|
| Jan | ₹20 |
| Feb | ₹10 |
| Mar | ₹30 |
| Apr | ₹5 |
| May | ₹70 |
| Jun | ₹30 |
| Jul | ₹40 |

### Results:

| Month | Sales | Running Total | Rolling Total (3 months) |
|-------|-------|---------------|-------------------------|
| Jan | ₹20 | ₹20 | ₹20 |
| Feb | ₹10 | ₹30 | ₹30 |
| Mar | ₹30 | ₹60 | ₹60 |
| Apr | ₹5 | ₹65 | ₹45 (dropped Jan) |
| May | ₹70 | ₹135 | ₹105 (dropped Feb) |
| Jun | ₹30 | ₹165 | ₹105 (dropped Mar) |
| Jul | ₹40 | ₹205 | ₹140 (dropped Apr) |

---

## The Magic Frame Clause

### For Running Total (Default):
```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```
**Meaning:** 
- Start from **first row ever** (UNBOUNDED PRECEDING)
- Go until **current row**
- This is automatic! You don't need to write it

### For Rolling Total (3 months):
```sql
ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
```
**Meaning:**
- Start from **2 rows before** (2 PRECEDING)
- Go until **current row**
- Total = 3 rows (2 before + 1 current)

---

## When to Use Each?

### Use Running Total When:
✅ Tracking total budget spent
✅ Counting total students enrolled since school started
✅ Your lifetime video game score
✅ Total money earned in your life

### Use Rolling Total When:
✅ Last 3 months of sales
✅ Your score in last 5 games
✅ Average temperature of last 7 days
✅ Recent performance (not all-time)

---

## Important Rules to Remember

1. **ORDER BY is MUST** - You need to sort data (usually by date/time)
2. **Running Total** = Keeps everything from start
3. **Rolling Total** = Fixed window that slides
4. Both help analyze **trends over time**
5. You can use any function: SUM, AVG, COUNT, MAX, MIN

---

## Other Examples

### Running Average (Instead of Total):
```sql
SELECT 
    month,
    sales,
    AVG(sales) OVER (ORDER BY month) AS running_average
FROM sales_data;
```

### Rolling Maximum (Last 3 months):
```sql
SELECT 
    month,
    sales,
    MAX(sales) OVER (
        ORDER BY month 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS rolling_max_3months
FROM sales_data;
```

---

## Practice Questions

1. What's the difference between running total and rolling total?
2. If you want to track your **all-time** high score, which would you use?
3. If you want to track your performance in the **last 5 games**, which would you use?
4. What does "2 PRECEDING" mean?
5. Why is ORDER BY important?

---

## Key Takeaway

- **Running Total** = Like a piggy bank that keeps growing 🐷💰
- **Rolling Total** = Like a sliding window that moves forward 🪟➡️

Both are super powerful for understanding how things change over time!
