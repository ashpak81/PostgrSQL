# 12. SQL Execution Plans (EXPLAIN / EXPLAIN ANALYZE)

> 🔑 **If you can read an execution plan confidently, you are ahead of ~95% candidates.**

Execution plans show **how the database actually executes your query**, not how you *think* it does.

---

## 1️⃣ What Is an Execution Plan?

An execution plan describes:

* How tables are accessed (Seq Scan, Index Scan, Bitmap Scan)
* Join strategy (Nested Loop, Hash Join, Merge Join)
* Estimated vs actual rows
* Cost-based decisions made by the planner

PostgreSQL uses a **cost-based optimizer**.

---

## 2️⃣ EXPLAIN

### 🔹 What It Does

* Shows **estimated plan only**
* Does NOT run the query
* Uses table statistics

### Syntax

```sql
EXPLAIN
SELECT * FROM users WHERE id = 101;
```

### Sample Output (simplified)

```
Index Scan using users_pkey on users
(cost=0.29..8.31 rows=1 width=64)
```

### Key Terms

* **cost** → startup_cost..total_cost
* **rows** → estimated rows
* **width** → average row size

🔑 Interview Line:

> “EXPLAIN shows the planner’s estimate without executing the query.”

---

## 3️⃣ EXPLAIN ANALYZE

### 🔹 What It Does

* Actually **executes the query**
* Shows **real execution time & actual rows**
* Best tool for performance debugging

### Syntax

```sql
EXPLAIN ANALYZE
SELECT * FROM users WHERE id = 101;
```

### Sample Output

```
Index Scan using users_pkey on users
(cost=0.29..8.31 rows=1 width=64)
(actual time=0.02..0.03 rows=1 loops=1)
```

🔑 Interview Line:

> “EXPLAIN ANALYZE is the truth; EXPLAIN is just a guess.”

⚠️ Warning:

* Executes query → avoid on heavy production queries

---

## 4️⃣ Sequential Scan (Seq Scan)

### 🔹 What It Is

* Reads **entire table row by row**

### When PostgreSQL Chooses Seq Scan

* Table is small
* Condition matches large % of rows
* No useful index exists

### Example

```sql
EXPLAIN ANALYZE
SELECT * FROM users WHERE status = 'ACTIVE';
```

Output:

```
Seq Scan on users
```

🔑 Interview Line:

> “Seq Scan is often faster than index scan for low-selectivity filters.”

---

## 5️⃣ Index Scan

### 🔹 What It Is

* Traverses index
* Fetches matching rows from table

### When Used

* High selectivity
* Small result set

### Example

```sql
EXPLAIN ANALYZE
SELECT * FROM users WHERE id = 101;
```

Output:

```
Index Scan using users_pkey on users
```

### Downsides

* Random I/O
* Heap fetch per row

---

## 6️⃣ Bitmap Index Scan (Very Important 🔥)

### 🔹 What It Is

Two-step process:

1. Bitmap Index Scan → collects row locations
2. Bitmap Heap Scan → fetches rows in batches

### When PostgreSQL Uses It

* Medium selectivity
* Multiple matching rows
* Multiple indexes combined

### Example

```sql
EXPLAIN ANALYZE
SELECT * FROM orders
WHERE status = 'PAID' AND created_at >= '2024-01-01';
```

Output:

```
Bitmap Index Scan on idx_orders_status
Bitmap Index Scan on idx_orders_created
Bitmap Heap Scan on orders
```

🔑 Interview Line:

> “Bitmap scans reduce random I/O by batching heap access.”

---

## 7️⃣ Seq Scan vs Index Scan vs Bitmap Scan

| Scan Type   | Best For     | Notes                            |
| ----------- | ------------ | -------------------------------- |
| Seq Scan    | Large % rows | Fast for small / low-selectivity |
| Index Scan  | Few rows     | Random I/O per row               |
| Bitmap Scan | Medium rows  | Efficient batch fetching         |

---

## 8️⃣ Estimated Rows vs Actual Rows (Critical 🔥)

Look for mismatches:

```sql
rows=10 (estimated)
rows=10000 (actual)
```

### Why It Matters

* Indicates outdated statistics
* Leads to bad plan choices

### Fix

```sql
ANALYZE table_name;
```

---

## 9️⃣ Common Red Flags in Plans

🚩 Seq Scan on huge table
🚩 Large difference in estimated vs actual rows
🚩 High execution time despite index

---

## 🔥 Interview One-Liners

* “PostgreSQL is cost-based, not rule-based.”
* “Seq Scan is not bad — it’s often optimal.”
* “Bitmap scans are used for medium selectivity.”
* “Always trust EXPLAIN ANALYZE over EXPLAIN.”

---

📌 **If you can explain why PostgreSQL chose a bitmap scan, you’re already senior-level.**
