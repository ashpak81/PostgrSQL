# 14. ACID Properties (Transaction Fundamentals 🔥)

> 🔑 **ACID is not theory — it explains why databases don’t corrupt data under failure.**

Interviewers ask ACID to test:

* Your understanding of **transactions**
* How databases behave under **failure & concurrency**
* Whether you’ve worked with **real systems**, not just queries

---

## What Is a Transaction?

A **transaction** is a sequence of operations treated as **one logical unit of work**.

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

Either **all operations succeed**, or **none do**.

---

## ACID Overview

| Property    | Meaning                         |
| ----------- | ------------------------------- |
| Atomicity   | All or nothing                  |
| Consistency | Valid state → valid state       |
| Isolation   | Transactions don’t interfere    |
| Durability  | Committed data survives crashes |

---

## 1️⃣ Atomicity

### 🔹 Definition

A transaction is **indivisible** — it either **fully commits** or **fully rolls back**.

---

### Example

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
-- crash happens here
ROLLBACK;
```

❌ Partial update is NOT allowed

---

### How DB Ensures Atomicity

* Undo logs / WAL (Write-Ahead Logging)
* Rollback segments

🔑 Interview Line:

> “Atomicity ensures no partial effects of a transaction are visible.”

---

## 2️⃣ Consistency

### 🔹 Definition

A transaction brings the database from **one valid state to another valid state**.

---

### Example

Rule: `account_balance >= 0`

```sql
UPDATE accounts SET balance = -500 WHERE id = 1;
```

❌ Violates consistency

---

### Who Enforces Consistency?

* Constraints (PK, FK, UNIQUE, CHECK)
* Triggers
* Application logic

🔑 Interview Line:

> “Consistency is enforced by constraints and business rules, not magic.”

---

## 3️⃣ Isolation

### 🔹 Definition

Concurrent transactions behave **as if executed serially**.

---

### Problems Without Isolation

| Problem             | Description                 |
| ------------------- | --------------------------- |
| Dirty Read          | Read uncommitted data       |
| Non-repeatable Read | Same row changes during txn |
| Phantom Read        | New rows appear             |

---

### Isolation Levels (Preview)

| Level            | Dirty | Non-repeatable | Phantom |
| ---------------- | ----- | -------------- | ------- |
| Read Uncommitted | ❌     | ❌              | ❌       |
| Read Committed   | ✅     | ❌              | ❌       |
| Repeatable Read  | ✅     | ✅              | ❌       |
| Serializable     | ✅     | ✅              | ✅       |

(PostgreSQL uses MVCC)

🔑 Interview Line:

> “Isolation controls visibility between concurrent transactions.”

---

## 4️⃣ Durability

### 🔹 Definition

Once a transaction commits, its changes **will not be lost**, even after a crash.

---

### How Durability Is Achieved

* Write-Ahead Logging (WAL)
* fsync to disk
* Replication (optional)

---

### Example

```sql
COMMIT;
-- power failure
```

✅ Data is recovered from WAL on restart

🔑 Interview Line:

> “Durability means committed data survives crashes.”

---

## ACID in Real Life (Simple Analogy)

💳 Bank Transfer:

* Atomicity → Money not half-transferred
* Consistency → Total money preserved
* Isolation → Parallel transfers don’t mix
* Durability → Transfer recorded permanently

---

## Common Interview Traps

❌ ACID is fully handled by application
❌ Isolation = locking only
❌ Consistency = same as isolation

✅ Correct:

* DB handles atomicity & durability
* Isolation via MVCC + locks
* Consistency via constraints

---

## 🔥 Interview One-Liners

* “Atomicity prevents partial transactions.”
* “Consistency is enforced by constraints.”
* “Isolation controls concurrency anomalies.”
* “Durability relies on WAL and disk persistence.”

---

📌 **If you can clearly separate all four ACID properties, you’re already ahead of most candidates.**
