# 09. SQL Composite Indexes (High-Impact Interview Topic)

Composite indexes are one of the **most misunderstood** topics in SQL interviews. Correct understanding here clearly separates **real backend engineers** from beginners.

---

## 🔑 What Interviewers Expect

* What a composite index is
* Why **column order matters**
* How the **left-most prefix rule** works
* When composite indexes help vs fail

---

## 1️⃣ What Is a Composite Index?

A **composite index** is an index created on **multiple columns together**.

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

🔑 **Interview Line**:

> “A composite index stores data sorted by the first column, then the second column, and so on.”

---

## 2️⃣ Why Composite Indexes Exist

### 🎯 Problem

Query filters on **multiple columns**:

```sql
SELECT *
FROM orders
WHERE user_id = 101 AND status = 'PAID';
```

❌ Single-column indexes are less efficient

### ✅ Composite index solution

```sql
(user_id, status)
```

One index lookup instead of two

---

## 3️⃣ Index Column Order (MOST IMPORTANT)

### ❗ Rule

> **The order of columns in a composite index determines which queries can use it.**

---

### Example Index

```sql
CREATE INDEX idx_orders_user_date
ON orders(user_id, created_at);
```

---

### ✅ Queries that CAN use this index

```sql
WHERE user_id = 10;
```

```sql
WHERE user_id = 10 AND created_at >= '2024-01-01';
```

```sql
WHERE user_id = 10 ORDER BY created_at DESC;
```

---

### ❌ Queries that CANNOT use this index

```sql
WHERE created_at >= '2024-01-01';
```

```sql
WHERE created_at >= '2024-01-01' AND status = 'PAID';
```

🔑 **Interview Line**:

> “If the first column of a composite index is not used, the index is useless.”

---

## 4️⃣ Left-Most Prefix Rule (Must Memorize)

A composite index can be used **only if the query uses the left-most columns in order**.

### Index

```sql
(user_id, created_at, status)
```

### Valid prefixes

* `(user_id)`
* `(user_id, created_at)`
* `(user_id, created_at, status)`

### Invalid usage

* `(created_at)` ❌
* `(created_at, status)` ❌
* `(status)` ❌

---

## 5️⃣ Equality vs Range Condition (Subtle but Critical)

### Index

```sql
(user_id, created_at)
```

### Query

```sql
WHERE user_id = 10
  AND created_at >= '2024-01-01';
```

✅ Index used efficiently

---

### ❌ Reverse conditions

```sql
WHERE user_id >= 10
  AND created_at = '2024-01-01';
```

Index usage stops after range condition

🔑 **Rule**:

> “Equality columns should come before range columns in a composite index.”

---

## 6️⃣ ORDER BY & Composite Index

### Index

```sql
(user_id, created_at DESC)
```

### Query

```sql
WHERE user_id = 10
ORDER BY created_at DESC;
```

✅ Avoids extra sorting

---

## 7️⃣ Composite Index vs Multiple Single Indexes

| Scenario                  | Better Choice    |
| ------------------------- | ---------------- |
| user_id AND status        | Composite index  |
| user_id OR status         | Separate indexes |
| Frequent combined filters | Composite        |

🔑 **Interview Line**:

> “Composite indexes are designed for combined filtering, not OR conditions.”

---

## 8️⃣ Common Mistakes & Better Alternatives

### ❌ Wrong column order

```sql
(status, user_id)
```

Query filters mostly by user_id ❌

### ✅ Correct

```sql
(user_id, status)
```

---

### ❌ Over-indexing

Too many composite indexes slow writes

### ✅ Solution

* Analyze real query patterns
* Use EXPLAIN ANALYZE

---

## 9️⃣ PostgreSQL Planner Behavior (Senior Insight)

* PostgreSQL may use **bitmap index scans** with multiple single-column indexes
* Composite index is still faster for selective queries

🔑 **Interview Line**:

> “Bitmap scans help, but composite indexes are still superior for highly selective filters.”

---

## 🔥 Interview One-Liners

* “Column order in composite indexes is critical.”
* “Left-most prefix rule decides index usability.”
* “Equality columns should come before range columns.”
* “Composite indexes optimize combined filters.”

---

## ✅ When to Use Composite Indexes

✔ Frequent queries filter on multiple columns
✔ Combined WHERE + ORDER BY
✔ High-selectivity first column

---

📌 **If you explain composite indexes correctly, you outperform ~85% SQL candidates.**
