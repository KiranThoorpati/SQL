# SQL WHERE Clause with AND Operator - Student Notes

## What is the WHERE Clause?

The WHERE clause in SQL is like a filter that helps us choose which rows we want to see from a table. It's like asking: "Show me only the data that matches my conditions."

**Rule:** ALL conditions in the WHERE clause must be TRUE to keep a row in the results.

## Multiple Conditions with AND Operator

Sometimes we need more than one condition to find exactly what we're looking for. When we have multiple conditions, we use logical operators to connect them.

### The AND Operator

The AND operator is very strict! It means **BOTH** conditions must be true for a row to stay in the results.

**Example:** Find customers from USA AND who have a score higher than 500.

## Step-by-Step Example

Let's say we have a customers table with this data:

| Name   | Country | Score |
|--------|---------|-------|
| Anna   | Germany | 350   |
| John   | USA     | 900   |
| George | Canada  | 750   |
| Martin | France  | 200   |
| Peter  | USA     | 0     |

### Our Task: Find customers from USA AND score > 500

**Step 1:** Start with basic SELECT
```sql
SELECT * FROM customers;
```
This gives us all customers.

**Step 2:** Add first condition (country = USA)
```sql
SELECT * FROM customers
WHERE country = 'USA';
```
This gives us John and Peter.

**Step 3:** Add second condition with AND operator
```sql
SELECT * FROM customers
WHERE country = 'USA' AND score > 500;
```

### How AND Operator Works - Row by Row Check:

**Anna (Germany, 350):**
- Condition 1: Country = USA? ❌ FALSE
- Condition 2: Score > 500? ❌ FALSE
- Result: FALSE AND FALSE = **REMOVED**

**John (USA, 900):**
- Condition 1: Country = USA? ✅ TRUE
- Condition 2: Score > 500? ✅ TRUE
- Result: TRUE AND TRUE = **KEPT IN RESULTS**

**George (Canada, 750):**
- Condition 1: Country = USA? ❌ FALSE
- Condition 2: Score > 500? ✅ TRUE
- Result: FALSE AND TRUE = **REMOVED**

**Martin (France, 200):**
- Condition 1: Country = USA? ❌ FALSE
- Condition 2: Score > 500? ❌ FALSE
- Result: FALSE AND FALSE = **REMOVED**

**Peter (USA, 0):**
- Condition 1: Country = USA? ✅ TRUE
- Condition 2: Score > 500? ❌ FALSE
- Result: TRUE AND FALSE = **REMOVED**

### Final Result:
Only **John** meets both conditions, so only John appears in our results.

## Key Points to Remember

1. **AND is very strict** - BOTH conditions must be true
2. If even ONE condition is false, the entire row is removed
3. AND operator connects multiple conditions in the WHERE clause
4. Use AND when you want data that meets ALL your requirements

## Practice Question

If you wanted to find customers from Canada AND with score less than 800, what would your SQL query look like?

**Answer:**
```sql
SELECT * FROM customers
WHERE country = 'Canada' AND score < 800;
```
