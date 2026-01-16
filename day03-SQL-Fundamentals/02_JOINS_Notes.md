# 2. SQL JOINS (Interview-Critical)

> **Goal:** Understand joins not just syntactically, but logically, with common bugs, performance impact, and safer alternatives.

---

## 2.1 What is a JOIN?

A JOIN combines rows from two or more tables based on a related column.

```text
Table A ⨝ Table B → Combined result set
```

🔑 **Interview Line:**

> A JOIN is a logical operation; performance depends on indexes and join conditions.

---

## 2.2 INNER JOIN

### Syntax

```sql
SELECT u.id, u.name, o.order_id
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

### Behavior

* Returns only **matching rows** from both tables
* Non-matching rows are discarded

### When to Use

* Mandatory relationships
* Child record must exist

🔑 **Senior Signal:**

> INNER JOIN is the safest and most performant join when nulls are not needed.

---

## 2.3 LEFT JOIN (Most Interview Bugs Here 🔥)

### Syntax

```sql
SELECT u.id, u.name, o.order_id
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

### Behavior

* Returns **all rows from left table**
* Matching rows from right table
* NULLs for non-matching right rows

---

## 2.4 LEFT JOIN + WHERE Bug ❌ (Very Common)

### ❌ Wrong

```sql
SELECT u.id, u.name
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.status = 'COMPLETED';
```

🚨 **Problem:**

* Converts LEFT JOIN into INNER JOIN
* Removes rows where `o.status` is NULL

---

### ✅ Correct Alternatives

#### Option 1: Move condition to ON clause (Recommended)

```sql
SELECT u.id, u.name
FROM users u
LEFT JOIN orders o
  ON u.id = o.user_id
 AND o.status = 'COMPLETED';
```

#### Option 2: Explicit NULL handling

```sql
WHERE o.status = 'COMPLETED' OR o.status IS NULL;
```

🔑 **Interview Line:**

> Conditions on the right table of a LEFT JOIN must go in the ON clause.

---

## 2.5 RIGHT JOIN

### Syntax

```sql
SELECT u.id, u.name, o.order_id
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

### Reality Check

* Rarely used in production
* Often rewritten as LEFT JOIN for readability

### ✅ Preferred Alternative

```sql
SELECT u.id, u.name, o.order_id
FROM orders o
LEFT JOIN users u ON u.id = o.user_id;
```

🔑 **Senior Insight:**

> LEFT JOIN improves readability; RIGHT JOIN is avoidable.

---

## 2.6 FULL JOIN

### Syntax

```sql
SELECT u.id, u.name, o.order_id
FROM users u
FULL JOIN orders o ON u.id = o.user_id;
```

### Behavior

* Includes all matching and non-matching rows from both tables

### Use Cases

* Data reconciliation
* Reporting mismatches

⚠️ **Performance Warning:**

* Expensive on large datasets

---

## 2.7 SELF JOIN

### Use Case

* Hierarchical data (manager → employee)

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

🔑 **Interview Tip:**

> Self joins model hierarchical relationships.

---

## 2.8 JOIN vs EXISTS (Very Important)

### ❌ JOIN (can cause duplicates)

```sql
SELECT u.id, u.name
FROM users u
JOIN orders o ON u.id = o.user_id;
```

### ✅ EXISTS (safer for existence checks)

```sql
SELECT u.id, u.name
FROM users u
WHERE EXISTS (
  SELECT 1
  FROM orders o
  WHERE o.user_id = u.id
);
```

### Why EXISTS is Better Here

* Stops on first match
* Avoids duplicate rows

🔑 **Interview Signal:**

> Use EXISTS when you only need to check presence, not fetch data.

---

## 2.9 JOIN Order & Performance

### Key Points

* Optimizer may reorder joins
* Large-to-small joins preferred
* Index on join columns is critical

🔑 **Senior Line:**

> Indexes on join keys matter more than join order.

---

## Common JOIN Interview Traps

| Trap                   | Reality              |
| ---------------------- | -------------------- |
| LEFT JOIN + WHERE      | ❌ Becomes INNER JOIN |
| Missing ON clause      | ❌ Cartesian product  |
| JOIN instead of EXISTS | ⚠️ Duplicates        |
| RIGHT JOIN usage       | ⚠️ Poor readability  |

---

## Mini Interview Question

> Find users who **never placed an order**.

### ✅ Answer

```sql
SELECT u.id, u.name
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.user_id IS NULL;
```

---

## Key Takeaways

* Most SQL bugs happen in JOIN logic
* LEFT JOIN conditions belong in ON clause
* EXISTS is safer for existence checks
* Readability matters in production SQL

> Mastering JOINs immediately separates strong candidates from average ones.
