# Comparing ROW_NUMBER, RANK, and DENSE_RANK

## Quick Comparison Chart

| Feature | ROW_NUMBER | RANK | DENSE_RANK |
|---------|------------|------|------------|
| **Unique Ranks?** | ✅ YES | ❌ NO | ❌ NO |
| **Handles Ties?** | ❌ NO | ✅ YES | ✅ YES |
| **Has Gaps?** | ❌ NO | ✅ YES | ❌ NO |

---

## Three Key Differences

### 1. Uniqueness of Ranks

**Question:** Does every row get a different number?

| Function | Answer | Example |
|----------|--------|---------|
| ROW_NUMBER | ✅ YES - All unique | 1, 2, 3, 4, 5 |
| RANK | ❌ NO - Can duplicate | 1, 2, 2, 4, 5 |
| DENSE_RANK | ❌ NO - Can duplicate | 1, 2, 2, 3, 4 |

**Winner:** Only ROW_NUMBER guarantees unique ranks!

---

### 2. Handling Ties

**Question:** If two rows have the same value, do they get the same rank?

| Function | Answer | Example with ties |
|----------|--------|-------------------|
| ROW_NUMBER | ❌ NO - Different ranks | 80→2, 80→3 |
| RANK | ✅ YES - Same ranks | 80→2, 80→2 |
| DENSE_RANK | ✅ YES - Same ranks | 80→2, 80→2 |

**Winner:** RANK and DENSE_RANK handle ties fairly!

---

### 3. Gaps in Numbering

**Question:** Can the function skip numbers?

| Function | Answer | Example |
|----------|--------|---------|
| ROW_NUMBER | ❌ NO gaps | 1, 2, 3, 4, 5 |
| RANK | ✅ YES - Skips after ties | 1, 2, 2, 4, 5 |
| DENSE_RANK | ❌ NO gaps | 1, 2, 2, 3, 4 |

**Winner:** Only RANK leaves gaps!

---

## Side-by-Side Example

### Data: Sales values
```
90, 60, 60, 60, 40, 20, 20, 20, 15, 10
```

### All Three Functions Compared:

| Sales | ROW_NUMBER | RANK | DENSE_RANK |
|-------|------------|------|------------|
| 90 | 1 | 1 | 1 |
| 60 | 2 | 2 | 2 |
| 60 | 3 | 2 | 2 |
| 60 | 4 | 2 | 2 |
| 40 | 5 | 5 | 3 |
| 20 | 6 | 6 | 4 |
| 20 | 7 | 6 | 4 |
| 20 | 8 | 6 | 4 |
| 15 | 9 | 9 | 5 |
| 10 | 10 | 10 | 6 |

---

## Visual Pattern Recognition

### ROW_NUMBER Pattern:
```
1, 2, 3, 4, 5, 6, 7, 8, 9, 10
↑
Always unique, always continuous
```

### RANK Pattern:
```
1, 2, 2, 2, 5, 6, 6, 6, 9, 10
      ↑         ↑         ↑
   Ties!    Skip 3,4   Skip 7,8
```

### DENSE_RANK Pattern:
```
1, 2, 2, 2, 3, 4, 4, 4, 5, 6
      ↑         ↑
   Ties!    No skip!
```

---

## Summary Table

| Aspect | ROW_NUMBER | RANK | DENSE_RANK |
|--------|------------|------|------------|
| Uniqueness | All different | Can repeat | Can repeat |
| Ties | Ignores ties | Shares ranks | Shares ranks |
| Gaps | Never | Yes (after ties) | Never |
| Ending value | = Total rows | = Total rows | = Unique values |
| Best for | Unique numbering | True position | Continuous levels |

---

## When to Use Each Function?

### Use ROW_NUMBER when:
- You need every row to have a unique number
- You're selecting "top N" items
- You don't care about ties
- You need consistent numbering
- Example: "Number all students 1 to 30"

### Use RANK when:
- Ties should share the same rank
- You want to show true positions
- Gaps after ties make sense
- Example: "Olympic medals - two golds, no silver, then bronze"

### Use DENSE_RANK when:
- Ties should share the same rank
- You DON'T want gaps
- You want continuous numbering
- Example: "Grade levels A, B, C with no skips"

---

## Quick Decision Tree

```
Do you need unique numbers for each row?
├─ YES → Use ROW_NUMBER
└─ NO → Do ties need the same rank?
    ├─ NO → Use ROW_NUMBER
    └─ YES → Should there be gaps after ties?
        ├─ YES → Use RANK
        └─ NO → Use DENSE_RANK
```

---

## Real-World Analogies

### ROW_NUMBER = Roll Numbers in Class
- Every student gets a unique number
- Even twins get different numbers
- 1, 2, 3, 4... no repeats

### RANK = Olympic Medals
- Two people can tie for gold
- No silver medal if two golds
- Next person gets bronze (3rd place)

### DENSE_RANK = Building Floors
- Multiple apartments per floor
- Floor numbers never skip
- Ground, 1st, 2nd, 3rd... continuous

---

## Most Commonly Used

According to the instructor:

**ROW_NUMBER is used most often!**

Why?
- Simple and predictable
- Always unique numbers
- Easy to work with
- Good for most basic ranking needs

---

## Practice Exercise

Given sales: 100, 80, 80, 50, 50, 50, 20

What would each function produce?

### ROW_NUMBER:
```
1, 2, 3, 4, 5, 6, 7
```

### RANK:
```
1, 2, 2, 4, 4, 4, 7
```

### DENSE_RANK:
```
1, 2, 2, 3, 3, 3, 4
```

---

## Key Takeaways

1. **Only ROW_NUMBER** gives completely unique ranks
2. **Only ROW_NUMBER** doesn't handle ties
3. **Only RANK** leaves gaps in numbering
4. **RANK and DENSE_RANK** both handle ties
5. **ROW_NUMBER and DENSE_RANK** both have no gaps
6. All three require ORDER BY
7. All three can use PARTITION BY
8. ROW_NUMBER is most commonly used

---

## Memory Trick

Think of the names:

- **ROW_NUMBER**: Numbers each ROW uniquely
- **RANK**: RANKs with gaps (like sports rankings)
- **DENSE_RANK**: DENSE means packed tight (no gaps!)

---

## Final Comparison in One Sentence

- **ROW_NUMBER**: Unique numbers, ignores ties, no gaps
- **RANK**: Shared ranks for ties, leaves gaps
- **DENSE_RANK**: Shared ranks for ties, no gaps

Choose based on what you need!
