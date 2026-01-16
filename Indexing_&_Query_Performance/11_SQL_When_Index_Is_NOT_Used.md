# 11. When an Index Is **NOT** Used (Critical Interview Topic ⚠️)

Many candidates know **how to create indexes**, but get rejected because they don’t know **why an index is ignored**. This topic is **pure interview gold**.

---

## 🔑 What Interviewers Are Really Testing

* Do you understand **query planner decisions**?
* Can you diagnose **slow queries despite indexes**?
* Do you know **real production pitfalls**?

---

## 1️⃣ Low Selectivity Columns

### 🔹 What Is Selectivity?

Selectivity = **how many unique values** a column has.

* High selectivity → many unique values (email, user_id)
* Low selectivity → few unique values (gender, status)

---

### ❌ Index on Low-Selectivity Column

```sql
CREATE INDEX idx_users_status ON users(status);
```

### Query

```sql
SELECT * FROM users WHERE status = 'ACTIVE';
```

❌ Index may be ignored

### ❓ Why?

If 90% rows are `ACTIVE`, scanning index + heap is **more expensive** than table scan.

🔑 **Interview Line**:

> “Indexes on low-selectivity columns are often ignored by the planner.”

---

### ✅ Better Alternatives

* Composite index:

```sql
(user_id, status)
```

* Partial index:

```sql
CREATE INDEX idx_active_users ON users(user_id)
WHERE status = 'ACTIVE';
```

---

## 2️⃣ Functions on Indexed Columns

### ❌ Index NOT used

```sql
SELECT * FROM users
WHERE LOWER(email) = 'abc@gmail.com';
```

❌ Function changes indexed value

---

### ✅ Alternatives

#### Option 1: Avoid function

```sql
WHERE email = 'abc@gmail.com';
```

#### Option 2: Functional Index

```sql
CREATE INDEX idx_users_lower_email
ON users (LOWER(email));
```

🔑 **Interview Line**:

> “Functions on indexed columns prevent index usage unless a functional index exists.”

---

## 3️⃣ Data Type Mismatch

### ❌ Index NOT used

```sql
-- user_id is INTEGER
SELECT * FROM orders WHERE user_id = '101';
```

❌ Implicit casting disables index

---

### ✅ Correct

```sql
SELECT * FROM orders WHERE user_id = 101;
```

🔑 **Interview Line**:

> “Implicit type casting can silently disable index usage.”

---

## 4️⃣ LIKE '%value' Pattern

### ❌ Index NOT used

```sql
SELECT * FROM users WHERE name LIKE '%john';
```

❌ Leading wildcard prevents B-tree traversal

---

### ✅ Index-friendly patterns

```sql
WHERE name LIKE 'john%';
```

---

### ✅ Alternatives for contains search

* PostgreSQL `GIN` + `pg_trgm`

```sql
CREATE EXTENSION pg_trgm;
CREATE INDEX idx_users_name_trgm
ON users USING GIN (name gin_trgm_ops);
```

🔑 **Interview Line**:

> “B-tree indexes cannot optimize leading wildcard searches.”

---

## 5️⃣ OR Conditions (Bonus Pitfall)

### ❌ Index often ignored

```sql
WHERE user_id = 10 OR status = 'ACTIVE';
```

### ✅ Better options

* Rewrite as UNION
* Use composite index if patterns allow

---

## 6️⃣ NOT EQUAL (!=, <>)

### ❌ Often avoids index

```sql
WHERE status != 'DELETED';
```

### ✅ Better

```sql
WHERE status IN ('ACTIVE', 'PENDING');
```

---

## 7️⃣ Small Tables

Even with an index, PostgreSQL may prefer **sequential scan** for small tables.

🔑 **Interview Line**:

> “For small tables, sequential scans are cheaper than index scans.”

---

## 8️⃣ How to Confirm Index Usage

```sql
EXPLAIN ANALYZE
SELECT ...;
```

Look for:

* `Index Scan`
* `Index Only Scan`
* ❌ `Seq Scan`

---

## 🔥 Interview One-Liners

* “Indexes are cost-based, not guaranteed.”
* “Low selectivity indexes are often ignored.”
* “Functions and type mismatches silently kill indexes.”
* “Leading wildcards disable B-tree indexes.”

---

## ✅ Summary Table

| Scenario           | Index Used? | Fix                       |
| ------------------ | ----------- | ------------------------- |
| Low selectivity    | ❌           | Composite / Partial index |
| Function on column | ❌           | Functional index          |
| Type mismatch      | ❌           | Correct data type         |
| LIKE '%abc'        | ❌           | Trigram / Full-text       |

---

📌 **If you can explain why an index is NOT used, you’re ahead of ~95% candidates.**
