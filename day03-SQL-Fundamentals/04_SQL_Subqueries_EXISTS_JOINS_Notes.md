# 4. Subqueries vs EXISTS vs JOIN (Performance & Correctness)

> **Goal:** Know when to use subqueries, EXISTS, or JOIN — and explain *why* in interviews.

---

## 4.1 What is a Subquery?

A subquery is a query nested inside another query.

```sql
SELECT name
FROM users
WHERE id IN (
  SELECT user_id FROM orders
);
```

🔑 **Interview Line:**

> Subqueries can be correlated or non-correlated, and their performance characteristics differ.

---

## 4.2 Non-Correlated Subqueries

### Example

```sql
SELECT name
FROM users
WHERE id IN (
  SELECT user_id FROM orders WHERE status = 'COMPLETED'
);
```

### Characteristics

* Inner query runs **once**
* Result reused by outer query
* Often optimized into a JOIN by the planner

### ❌ Common Pitfall: `NOT IN` with NULL

```sql
SELECT name
FROM users
WHERE id NOT IN (
  SELECT user_id FROM orders
);
```

🚨 **Problem:**

* If subquery returns NULL → query returns **no rows**

### ✅ Safer Alternative

```sql
SELECT name
FROM users u
WHERE NOT EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

🔑 **Senior Signal:**

> Avoid NOT IN if the subquery can return NULLs.

---

## 4.3 Correlated Subqueries

### Example

```sql
SELECT u.id, u.name
FROM users u
WHERE (
  SELECT COUNT(*)
  FROM orders o
  WHERE o.user_id = u.id
) > 5;
```

### Characteristics

* Inner query runs **once per outer row**
* Can be slow on large datasets

### ❌ Performance Issue

* N + 1 query pattern

### ✅ Better Alternative (JOIN + GROUP BY)

```sql
SELECT u.id, u.name
FROM users u
JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name
HAVING COUNT(*) > 5;
```

🔑 **Interview Line:**

> Correlated subqueries can degrade performance due to repeated execution.

---

## 4.4 EXISTS (Highly Preferred in Interviews 🔥)

### Syntax

```sql
SELECT u.id, u.name
FROM users u
WHERE EXISTS (
  SELECT 1
  FROM orders o
  WHERE o.user_id = u.id
);
```

### Why EXISTS Is Powerful

* Stops at first match
* Avoids duplicates
* Handles NULLs safely

🔑 **Senior Insight:**

> EXISTS is ideal when checking for presence, not fetching data.

---

## 4.5 JOIN vs EXISTS

### ❌ JOIN (may create duplicates)

```sql
SELECT u.id
FROM users u
JOIN orders o ON u.id = o.user_id;
```

### ✅ EXISTS (no duplicates)

```sql
SELECT u.id
FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

### When JOIN Is Better

* When you need columns from both tables
* When aggregating data

🔑 **Interview Line:**

> JOIN retrieves data; EXISTS checks existence.

---

## 4.6 Scalar Subqueries

### Example

```sql
SELECT u.id, u.name,
       (SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id) AS order_count
FROM users u;
```

### Reality

* Runs per row
* Easy to write, risky at scale

### ✅ Better Alternative (JOIN + GROUP BY)

```sql
SELECT u.id, u.name, COUNT(o.id) AS order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;
```

---

## 4.7 Subquery in FROM (Derived Tables)

```sql
SELECT city, COUNT(*)
FROM (
  SELECT DISTINCT city FROM users
) t
GROUP BY city;
```

### Notes

* Useful for readability
* Often replaced by CTEs

🔑 **Senior Tip:**

> Prefer CTEs over deeply nested subqueries for clarity.

---

## Common Interview Traps

| Trap                         | Reality       |
| ---------------------------- | ------------- |
| NOT IN with NULL             | ❌ Bug         |
| Correlated subquery at scale | ❌ Slow        |
| JOIN for existence           | ⚠️ Duplicates |
| Scalar subquery per row      | ❌ N+1         |

---

## Mini Interview Questions

### Q1. Find users who never placed an order

```sql
SELECT u.id, u.name
FROM users u
WHERE NOT EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

### Q2. Find users with at least one completed order

```sql
SELECT u.id, u.name
FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o
  WHERE o.user_id = u.id AND o.status = 'COMPLETED'
);
```

---

## Key Takeaways

* Prefer EXISTS for presence checks
* Avoid NOT IN when NULLs are possible
* Be cautious with correlated subqueries
* Optimize scalar subqueries with JOINs

> Mastering this topic signals strong SQL judgment and performance awareness.
