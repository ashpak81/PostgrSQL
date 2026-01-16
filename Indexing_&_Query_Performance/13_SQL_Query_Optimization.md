# 13. SQL Query Optimization (Think Like a Database 🔥)

> 🔑 **Query optimization is NOT about tricks — it’s about understanding how the planner thinks.**

Interviewers use this topic to test:

* Real production experience
* Performance intuition
* Ability to debug slow queries

---

## 1️⃣ Reducing Full Table Scans

### ❓ Why Full Table Scans Happen

* No usable index
* Low selectivity conditions
* Planner estimates scan is cheaper

---

### ❌ Bad Query (forces Seq Scan)

```sql
SELECT * FROM orders WHERE status = 'PAID';
```

(If 80% rows are PAID)

---

### ✅ Better Approaches

#### Option 1: Composite Index

```sql
CREATE INDEX idx_orders_status_date
ON orders(status, created_at);
```

#### Option 2: Partial Index

```sql
CREATE INDEX idx_orders_paid
ON orders(created_at)
WHERE status = 'PAID';
```

🔑 Interview Line:

> “Reducing full table scans often means improving selectivity, not forcing indexes.”

---

## 2️⃣ Query Refactoring (High Impact)

### ❌ Subquery Anti-Pattern

```sql
SELECT * FROM users u
WHERE u.id IN (
  SELECT user_id FROM orders WHERE amount > 1000
);
```

---

### ✅ Refactored Using JOIN

```sql
SELECT DISTINCT u.*
FROM users u
JOIN orders o ON o.user_id = u.id
WHERE o.amount > 1000;
```

### Why JOIN Is Better

* Better planner optimization
* Index-friendly
* Avoids nested execution

🔑 Interview Line:

> “Most IN subqueries can be rewritten as JOINs for better performance.”

---

## 3️⃣ Avoiding SELECT *

### ❌ Problem

```sql
SELECT * FROM users WHERE id = 10;
```

### Issues

* Unnecessary I/O
* Prevents index-only scans

---

### ✅ Better

```sql
SELECT id, email FROM users WHERE id = 10;
```

🔑 Interview Line:

> “SELECT * increases I/O and blocks index-only scans.”

---

## 4️⃣ Join Order Impact (Very Important 🔥)

### ❓ Does Join Order Matter?

* PostgreSQL can reorder joins
* BUT bad filters can mislead planner

---

### ❌ Bad Filtering Order

```sql
SELECT *
FROM orders o
JOIN users u ON o.user_id = u.id
WHERE u.country = 'IN';
```

(orders = huge, users = small)

---

### ✅ Better (Filter Early)

```sql
SELECT *
FROM users u
JOIN orders o ON o.user_id = u.id
WHERE u.country = 'IN';
```

🔑 Interview Line:

> “Filtering early reduces intermediate result size and join cost.”

---

## 5️⃣ Cardinality & Selectivity (Core Concepts)

### 🔹 Cardinality

* Number of rows returned by a query

### 🔹 Selectivity

* How well a condition filters rows

---

### Examples

| Column | Cardinality | Selectivity |
| ------ | ----------- | ----------- |
| id     | High        | High        |
| email  | High        | High        |
| status | Low         | Low         |
| gender | Low         | Low         |

---

### Why Planner Cares

* Chooses join type
* Chooses scan type
* Chooses index usage

🔑 Interview Line:

> “Indexes work best on high-selectivity columns.”

---

## 6️⃣ AND vs OR Conditions

### ❌ OR hurts index usage

```sql
WHERE status = 'PAID' OR amount > 1000;
```

### ✅ Rewrite

```sql
SELECT ... WHERE status = 'PAID'
UNION ALL
SELECT ... WHERE amount > 1000;
```

---

## 7️⃣ LIMIT Helps Optimization

```sql
SELECT * FROM orders
ORDER BY created_at DESC
LIMIT 10;
```

### Why It Helps

* Allows early termination
* Works well with index on `created_at`

---

## 8️⃣ Statistics Matter

Outdated stats = bad plans

```sql
ANALYZE orders;
```

🔑 Interview Line:

> “Good statistics are as important as good indexes.”

---

## 🔥 Interview One-Liners

* “Optimization starts with reducing rows early.”
* “Indexes don’t speed up bad queries.”
* “Planner decisions depend on cardinality and selectivity.”
* “Refactoring queries often gives bigger wins than new indexes.”

---

📌 **If you can explain cardinality vs selectivity clearly, interviewers will trust your performance intuition.**
