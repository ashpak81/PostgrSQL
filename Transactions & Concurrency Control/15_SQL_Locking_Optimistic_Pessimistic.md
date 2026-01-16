# 15. SQL Locking: Optimistic vs Pessimistic & SELECT ... FOR UPDATE 🔒

> 🔑 **Locking is where real concurrency and transaction understanding is tested in interviews.**

Locking ensures **data integrity** when multiple transactions access the same data concurrently.

---

## 1️⃣ Pessimistic Locking

### 🔹 Definition

* Assumes conflicts **will happen**
* Locks rows **before modification**
* Other transactions wait until lock released

### Syntax Example (PostgreSQL)

```sql
BEGIN;
SELECT * FROM accounts
WHERE id = 1
FOR UPDATE;
-- perform update safely
COMMIT;
```

### Use Case

* High contention tables
* Banking transfers
* Inventory systems

### Pros / Cons

| Pros                       | Cons                  |
| -------------------------- | --------------------- |
| Prevents conflicts upfront | Can lead to deadlocks |
| Simple to reason about     | Reduces concurrency   |

🔑 Interview Line:

> “Pessimistic locking locks rows before writing to prevent conflicts.”

---

## 2️⃣ Optimistic Locking

### 🔹 Definition

* Assumes conflicts **rarely happen**
* Checks for conflicts at **commit time**
* Usually uses a **version/timestamp column**

### Implementation Example

```sql
-- table accounts has version column
UPDATE accounts
SET balance = balance - 100, version = version + 1
WHERE id = 1 AND version = 5;
```

* If affected rows = 0 → conflict detected → retry

### Use Case

* Low contention tables
* Web applications with multiple users reading/writing

### Pros / Cons

| Pros             | Cons                  |
| ---------------- | --------------------- |
| High concurrency | Requires retry logic  |
| Non-blocking     | Slightly more complex |

🔑 Interview Line:

> “Optimistic locking detects conflicts at commit time using a version or timestamp column.”

---

## 3️⃣ SELECT ... FOR UPDATE

### 🔹 Definition

* Locks selected rows **for update** in current transaction
* Prevents other transactions from modifying them
* Other transactions **wait** until commit or rollback

### Example

```sql
BEGIN;
SELECT balance FROM accounts
WHERE id = 1
FOR UPDATE;
UPDATE accounts SET balance = balance - 100
WHERE id = 1;
COMMIT;
```

### Notes

* Works only within **transaction block**
* Locks **rows, not table** (row-level lock)
* Prevents **lost updates** in concurrent transactions

---

## 4️⃣ Deadlock Awareness

Pessimistic locking can lead to deadlocks:

```text
Txn1 locks row A -> waits for row B
Txn2 locks row B -> waits for row A
```

* PostgreSQL detects deadlocks and aborts one txn
* Always lock rows in **consistent order**

🔑 Interview Line:

> “Deadlocks are a risk of pessimistic locking and require careful row order or retry logic.”

---

## 5️⃣ Summary Table

| Concept               | Locking Type            | Use Case                       | Notes                         |
| --------------------- | ----------------------- | ------------------------------ | ----------------------------- |
| Pessimistic           | Row locked before write | High contention                | Risk of deadlocks             |
| Optimistic            | Version/timestamp check | Low contention                 | Non-blocking, needs retry     |
| SELECT ... FOR UPDATE | Row-level lock          | Safe update within transaction | Works only inside transaction |

---

## 🔥 Interview One-Liners

* “Pessimistic locks rows up front; optimistic checks at commit.”
* “SELECT ... FOR UPDATE prevents lost updates.”
* “Deadlocks occur when multiple transactions lock resources in different orders.”
* “Optimistic locking is ideal for high-read, low-write workloads.”

---

📌 **If you can draw a deadlock scenario with FOR UPDATE locks, you’re ahead of ~90% SQL candidates.**
