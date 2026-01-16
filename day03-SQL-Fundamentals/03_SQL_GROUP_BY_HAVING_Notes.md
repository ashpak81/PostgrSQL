# 3. GROUP BY & HAVING (Aggregation Traps)

> **Goal:** Master aggregation logic, avoid classic SQL mistakes, and confidently explain behavior in interviews.

---

## 3.1 What is GROUP BY?

`GROUP BY` groups rows that share the same values into summary rows.

```sql
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id;
```

🔑 **Interview Line:**

> GROUP BY reduces rows by aggregation.

---

## 3.2 Aggregation Functions

Common aggregate functions:

* `COUNT()`
* `SUM()`
* `AVG()`
* `MIN()`
* `MAX()`

```sql
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id;
```

---

## 3.3 GROUP BY Rule (Interview Favorite 🔥)

### ❌ Invalid Query

```sql
SELECT department_id, salary
FROM employees
GROUP BY department_id;
```

🚨 **Why it fails:**

* `salary` is neither aggregated nor part of `GROUP BY`

### ✅ Correct Alternatives

#### Option 1: Add column to GROUP BY

```sql
SELECT department_id, salary
FROM employees
GROUP BY department_id, salary;
```

#### Option 2: Aggregate the column (Recommended)

```sql
SELECT department_id, MAX(salary)
FROM employees
GROUP BY department_id;
```

🔑 **Interview Line:**

> Every selected column must be aggregated or present in GROUP BY.

---

## 3.4 WHERE vs HAVING (Very Important)

### Execution Order Reminder

```text
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

### ❌ Wrong Usage

```sql
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id
WHERE COUNT(*) > 5;
```

### ✅ Correct Usage

```sql
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;
```

🔑 **Interview Line:**

> WHERE filters rows, HAVING filters aggregated results.

---

## 3.5 WHERE + GROUP BY (Correct Pattern)

```sql
SELECT department_id, COUNT(*)
FROM employees
WHERE status = 'ACTIVE'
GROUP BY department_id;
```

### Why This Is Correct

* WHERE reduces rows **before aggregation**
* Improves performance

🔑 **Senior Signal:**

> Filter as early as possible using WHERE.

---

## 3.6 HAVING Without GROUP BY ⚠️

```sql
SELECT COUNT(*)
FROM employees
HAVING COUNT(*) > 100;
```

### Reality

* Valid SQL
* Rarely used
* Confuses readers

🔑 **Interview Tip:**

> Technically valid but avoided for clarity.

---

## 3.7 COUNT(*) vs COUNT(column)

```sql
COUNT(*)      -- counts all rows
COUNT(email)  -- ignores NULL values
```

### Example

If 50% emails are NULL:

* `COUNT(*)` → total rows
* `COUNT(email)` → half rows

### ✅ Alternative (If NULLs must count)

```sql
COUNT(COALESCE(email, ''))
```

🔑 **Interview Favorite:**

> COUNT(column) ignores NULLs.

---

## 3.8 DISTINCT vs GROUP BY

### ❌ Overusing DISTINCT

```sql
SELECT DISTINCT department_id FROM employees;
```

### ✅ Better Alternative

```sql
SELECT department_id FROM employees GROUP BY department_id;
```

### When DISTINCT Is Acceptable

* Simple de-duplication
* Small datasets

🔑 **Senior Insight:**

> Frequent DISTINCT may indicate join or design issues.

---

## 3.9 GROUP BY with JOINs (Common Trap)

### ❌ Problematic Query

```sql
SELECT u.id, COUNT(o.id)
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id;
```

🚨 **Issue:**

* NULL handling may mislead counts

### ✅ Safer Alternative

```sql
SELECT u.id, COUNT(o.id)
FROM users u
LEFT JOIN orders o
  ON u.id = o.user_id
 AND o.status = 'COMPLETED'
GROUP BY u.id;
```

🔑 **Interview Line:**

> Always be careful with LEFT JOIN + aggregation.

---

## Common GROUP BY Interview Traps

| Trap                         | Reality   |
| ---------------------------- | --------- |
| Selecting non-grouped column | ❌ Invalid |
| WHERE on aggregate           | ❌ Invalid |
| HAVING instead of WHERE      | ⚠️ Slower |
| COUNT(col) confusion         | ❌ Bug     |

---

## Mini Interview Questions

### Q1. Find departments with more than 10 employees

```sql
SELECT department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 10;
```

### Q2. Count active users per city

```sql
SELECT city, COUNT(*)
FROM users
WHERE status = 'ACTIVE'
GROUP BY city;
```

---

## Key Takeaways

* GROUP BY controls aggregation granularity
* WHERE filters rows, HAVING filters groups
* Always aggregate or group selected columns
* Misusing HAVING is a performance smell

> Mastering aggregation eliminates many real-world SQL bugs and boosts interview confidence.
