# Window Ranking Functions - Complete Summary Notes

## What Are Window Ranking Functions?

Window ranking functions **assign ranks (positions) to each row** in your data based on certain rules.

Think of it like giving positions to students in a race - 1st place, 2nd place, 3rd place, etc.! 🏃‍♂️🏃‍♀️

---

## Two Types of Ranking

### 📊 Type 1: Integer-Based Ranking
**Assigns whole numbers (1, 2, 3, 4...) to each row**

**Four Functions:**

1. **ROW_NUMBER** - Gives unique numbers to each row
2. **RANK** - Gives same rank to ties, then skips numbers
3. **DENSE_RANK** - Gives same rank to ties, no skipping
4. **NTILE** - Divides data into equal groups/buckets

---

### 📈 Type 2: Percentage-Based Ranking
**Calculates ranks as percentages or decimals (0.0 to 1.0)**

**Two Functions:**

1. **CUME_DIST** (Cumulative Distribution) - Shows what % of rows are equal or below
2. **PERCENT_RANK** - Shows relative position as a percentage

---

## Important Syntax Rules ⚠️

### ✅ Must Follow:
- **Empty expression** - Don't put anything inside the function brackets (except NTILE)
- **ORDER BY is REQUIRED** - Must sort your data
- **No FRAME clause allowed** - Cannot customize the window frame

### Example:
```sql
ROW_NUMBER() OVER (ORDER BY sales DESC)  ✅ Correct
ROW_NUMBER(sales) OVER (ORDER BY sales DESC)  ❌ Wrong!
```

---

## Real-World Uses (Where We Use These Functions)

### 🎯 Use Case 1: Top N Analysis
**Find the best or worst performers**

**Examples:**
- Top 5 students with highest marks
- Top 10 products with most sales
- Bottom 3 employees with lowest attendance

---

### 🎯 Use Case 2: Find and Remove Duplicates
**Clean up messy data using ROW_NUMBER**

**Example:** If you have duplicate customer records, you can:
1. Use ROW_NUMBER to identify duplicates
2. Keep only the first occurrence
3. Delete the rest

This helps **improve data quality**! 🧹

---

### 🎯 Use Case 3: Generate Unique IDs
**Create unique identifiers when your table doesn't have them**

**Why needed?**
- Sometimes tables don't have proper ID columns
- Use ROW_NUMBER to create unique numbers for each row
- Also useful for **pagination** (showing data page by page)

---

### 🎯 Use Case 4: Data Segmentation
**Group data into categories using NTILE**

**Examples:**
- Segment customers into VIP, Medium, Low spenders
- Group products into High, Medium, Low price ranges
- Divide employees by salary levels

---

### 🎯 Use Case 5: Data Distribution Analysis
**Understand how data is spread using CUME_DIST and PERCENT_RANK**

**Example:**
- See how student scores are distributed
- Compare one student's position to all others
- Understand if data is evenly spread or clustered

---

### 🎯 Use Case 6: Load Balancing (For Data Engineers)
**Split large data transfers into smaller chunks using NTILE**

**Example:**
- Moving 1 million records from one database to another
- Split into 10 groups of 100,000 each
- Transfer one group at a time safely

---

## Quick Function Comparison Table

| Function | Returns | Best For |
|----------|---------|----------|
| **ROW_NUMBER** | 1, 2, 3, 4... | Unique numbering |
| **RANK** | 1, 2, 2, 4... | Ranking with gaps |
| **DENSE_RANK** | 1, 2, 2, 3... | Ranking without gaps |
| **NTILE** | 1, 1, 2, 2, 3, 3... | Dividing into groups |
| **CUME_DIST** | 0.25, 0.50, 0.75... | Percentage position |
| **PERCENT_RANK** | 0.0, 0.33, 0.67... | Relative rank % |

---

## Summary in Simple Words

**Window Ranking Functions** are like giving report cards to students! 📝

- Some functions give **position numbers** (1st, 2nd, 3rd)
- Some functions give **percentage scores** (top 25%, top 50%)
- They help us find **winners and losers**
- They help us **organize and clean** our data
- They help us **understand patterns** in our information

---

## What's Next?

After ranking functions, we'll learn about **Value Window Functions** - these help us look at other rows and compare values! 👀

Think of it like looking at your neighbor's answer sheet to compare! (But this time it's allowed! 😄)

---

**Remember:** Ranking functions are super useful for analyzing data and finding important patterns. Practice them, and you'll become a data expert! 🌟
