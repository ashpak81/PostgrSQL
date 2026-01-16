# 10. SQL Covering Indexes & Index-Only Scans (Advanced Performance Topic 🔥)

Covering indexes and index-only scans are **advanced performance concepts** that strongly signal **real-world database experience**. Many candidates know indexes, **very few know this well**.

---

## 🔑 What Interviewers Are Testing

* Do you know how to **avoid hitting the table**?
* Do you understand **INCLUDE columns** in PostgreSQL?
* Do you know **when index-only scans actually work**?

---

## 1️⃣ What Is a Covering Index?

A **covering index** is an index that contains **all columns required by a query**, so the database **does not need to read the table (heap)**.

🔑 **Interview Line**:

> “A covering index allows the query to be satisfied entirely from the index.”

---

## 2️⃣ The Core Idea (Very Important)

### ❌ Normal Index Scan

1. Search index
2. Get row pointer
3. Fetch row from table (heap access)

### ✅ Covering Index

1. Search index
2. Get all required columns from index
3. **No table access** 🚀

---

## 3️⃣ PostgreSQL: INCLUDE Columns

PostgreSQL supports **non-key columns** in indexes using `INCLUDE`.

```sql
CREATE INDEX idx_orders_user_covering
ON orders(user_id)
INCLUDE (status, amount);
```

* `user_id` → indexed (sorted key)
* `status, amount` → stored only at leaf level

🔑 **Interview Line**:

> “INCLUDE columns don’t affect index ordering; they exist only to cover queries.”

---

## 4️⃣ Example: Why INCLUDE Matters

### Query

```sql
SELECT status, amount
FROM orders
WHERE user_id = 101;
```

### ❌ Index without INCLUDE

```sql
(user_id)
```

➡️ Index scan + heap fetch

### ✅ Covering index

```sql
(user_id) INCLUDE (status, amount)
```

➡️ Index-only scan possible

---

## 5️⃣ Index-Only Scan (Critical Concept)

An **index-only scan** occurs when:

1. All required columns are in the index
2. PostgreSQL can confirm row visibility via **visibility map**

```sql
EXPLAIN ANALYZE
SELECT status, amount
FROM orders
WHERE user_id = 101;
```

Look for:

```text
Index Only Scan using idx_orders_user_covering
```

---

## 6️⃣ Visibility Map (Why Index-Only Scan May Fail)

Even with a covering index, PostgreSQL may still hit the table.

### Why?

* PostgreSQL must check **row visibility** (MVCC)
* If visibility map is not set → heap access required

🔑 **Interview Line**:

> “Index-only scans depend on the visibility map, not just the index definition.”

---

## 7️⃣ VACUUM & Index-Only Scans

* `VACUUM` updates visibility map
* Frequent writes reduce index-only scan usage

🔑 **Senior Insight**:

> “Read-heavy systems benefit most from index-only scans.”

---

## 8️⃣ INCLUDE vs Composite Index (Very Common Confusion)

| Feature              | Composite Index | INCLUDE |
| -------------------- | --------------- | ------- |
| Affects ordering     | ✅ Yes           | ❌ No    |
| Used for filtering   | ✅ Yes           | ❌ No    |
| Used for covering    | ⚠️ Maybe        | ✅ Yes   |
| Increases index size | ⚠️ Moderate     | ⚠️ Low  |

🔑 **Interview Line**:

> “Composite indexes filter, INCLUDE columns cover.”

---

## 9️⃣ When NOT to Use INCLUDE

❌ Large text / JSON columns
❌ Columns rarely selected
❌ Write-heavy tables

---

## 🔟 Common Mistakes & Better Choices

### ❌ Using INCLUDE to filter

```sql
WHERE status = 'PAID'
```

Index on `(user_id) INCLUDE (status)` ❌

### ✅ Correct

```sql
(user_id, status)
```

---

## 🔥 Interview One-Liners

* “Covering indexes avoid heap access.”
* “INCLUDE columns exist only to satisfy SELECT lists.”
* “Index-only scans depend on the visibility map.”
* “INCLUDE does not affect index ordering.”

---

## ✅ When to Use Covering Indexes

✔ Read-heavy systems
✔ High-frequency queries
✔ Narrow SELECT lists
✔ Performance-critical paths

---

📌 **If you understand index-only scans clearly, you’re ahead of ~90% SQL candidates.**
