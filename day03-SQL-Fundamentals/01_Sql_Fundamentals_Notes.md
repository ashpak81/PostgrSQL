# 1. SQL Fundamentals (Must-Have)

> **Goal:** Build strong fundamentals with interview-level depth and real-world understanding.

---

## 1.1 SELECT Statement

### Basic Usage
```sql
SELECT id, name, email FROM users;
```

### Best Practices
- Avoid `SELECT *` in production
- Fetch only required columns

### Why `SELECT *` is discouraged 🔑
- Unnecessary I/O
- Breaks when schema changes
- Prevents index-only scans

### ✅ Better Alternative
```sql
SELECT id, name, email FROM users;
```
- Fetch only required columns
- Safer for schema changes
- Enables index-only scans when possible

**Interview Line:**
> “I explicitly select required columns for performance and maintainability.”

---

## 1.2 WHERE Clause

### Filtering Rows
```sql
SELECT id, name FROM users WHERE status = 'ACTIVE';
```

### Important Points
- `WHERE` filters rows **before grouping**
- Index usage is driven mainly by `WHERE`

### Index Pitfall ❌
```sql
WHERE LOWER(email) = 'test@gmail.com';
```

### ✅ Better Alternatives
```sql
WHERE email = 'test@gmail.com';
```
OR
```sql
CREATE INDEX idx_users_email_lower ON users (LOWER(email));
```

🔑 **Senior Signal:**
> Avoid functions on indexed columns unless a functional index exists.

---

## 1.3 ORDER BY

```sql
SELECT id, name FROM users ORDER BY created_at DESC;
```

### Performance Considerations
- Sorting is expensive
- Large sorts may spill to disk
- Index can avoid sort **only if order matches index**

🔑 **Interview Tip:**
> ORDER BY without LIMIT on large tables is dangerous.

### ✅ Better Alternatives
```sql
-- Use index-aligned ordering
SELECT id, name FROM users ORDER BY created_at DESC LIMIT 50;
```
- Add index on `(created_at DESC)`
- Always combine `ORDER BY` with `LIMIT` when possible

---

## 1.4 LIMIT & OFFSET

```sql
SELECT id, name FROM users ORDER BY id LIMIT 10 OFFSET 100;
```

### Key Points
- OFFSET forces DB to scan and discard rows
- Performance degrades as OFFSET grows

🔑 **Senior Insight:**
> OFFSET-based pagination does not scale.

### ✅ Better Alternative (Keyset Pagination)
```sql
SELECT id, name
FROM users
WHERE id > :last_seen_id
ORDER BY id
LIMIT 10;
```
- No row skipping
- Consistent performance at scale

---

## 1.5 DISTINCT

```sql
SELECT DISTINCT city FROM users;
```

### Things to Remember
- Uses sort or hash internally
- Expensive on large datasets
- Often hides bad joins or poor design

🔑 **Interview Tip:**
> Frequent DISTINCT usage indicates a possible design issue.

### ✅ Better Alternatives
- Fix JOIN conditions
- Use proper GROUP BY
```sql
SELECT city FROM users GROUP BY city;
```
- Normalize data to avoid duplicates

---

## 1.6 NULL Handling (Very Important)

### NULL Rules
- `NULL ≠ NULL`

❌ Wrong
```sql
WHERE email = NULL;
```

✅ Correct
```sql
WHERE email IS NULL;
```

### COUNT Behavior
```sql
COUNT(*)      -- counts all rows
COUNT(email)  -- ignores NULL values
```

🔑 **Interview Favorite:**
> COUNT(column) does not count NULLs.

### ✅ Better Alternatives
```sql
SELECT COUNT(*) FROM users;
```
OR
```sql
SELECT COUNT(COALESCE(email, '')) FROM users;
```

---

## 1.7 CASE WHEN

```sql
SELECT id,
       CASE
         WHEN score >= 90 THEN 'A'
         WHEN score >= 75 THEN 'B'
         ELSE 'C'
       END AS grade
FROM exams;
```

### Real-World Usage
- Conditional logic
- Business rules
- Reporting & dashboards

🔑 **Interview Line:**
> CASE is heavily used in real-world reporting.

---

## 1.8 SQL Logical Execution Order 🔥

```text
FROM
→ WHERE
→ GROUP BY
→ HAVING
→ SELECT
→ ORDER BY
→ LIMIT
```

### Why HAVING Exists
- `WHERE` filters rows
- `HAVING` filters aggregated results

🔑 **Senior Signal:**
> HAVING works after aggregation.

---

## Common Interview Traps (Fundamentals)

| Trap | Reality |
|-----|--------|
| WHERE after GROUP BY | ❌ Invalid |
| HAVING without aggregation | ⚠️ Allowed but rare |
| NOT IN with NULL | ❌ Bug-prone |
| COUNT(col) = COUNT(*) | ❌ False |

---

## Mini Interview Question

```sql
SELECT COUNT(email) FROM users;
```
**Question:** What happens if 50% of emails are NULL?

✅ **Answer:** COUNT(email) ignores NULL values.

---

## Key Takeaways
- SQL is about **behavior**, not syntax
- Index usage depends heavily on query structure
- NULL handling is a common interview filter
- Always explain **WHY**, not just **HOW**

> Mastering these fundamentals already puts you ahead of many candidates.
