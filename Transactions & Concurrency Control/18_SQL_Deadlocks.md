# 18. SQL Deadlocks (Detection & Resolution 🔥)

> 🔑 **If you can draw a deadlock scenario and explain how the DB resolves it, you’re ahead of ~90% candidates.**

Deadlocks are a **real production problem** in concurrent systems using locks.

---

## 1️⃣ What Is a Deadlock?

A **deadlock** occurs when two or more transactions:

* Each hold a lock
* Each wait for a lock held by another
* Result: **circular wait**, no progress possible

---

## 2️⃣ How Deadlocks Occur (Classic Scenario)

### Example: Two Transactions, Two Rows

**Transaction A**

```sql
BEGIN;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE; -- locks row 1
-- waits for row 2
SELECT * FROM accounts WHERE id = 2 FOR UPDATE;
```

**Transaction B**

```sql
BEGIN;
SELECT * FROM accounts WHERE id = 2 FOR UPDATE; -- locks row 2
-- waits for row 1
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
```

### 🔄 Deadlock Cycle

```
Txn A holds lock on row 1 → waits for row 2
Txn B holds lock on row 2 → waits for row 1
```

❌ Neither transaction can proceed

🔑 Interview Line:

> “Deadlocks occur due to circular wait on locked resources.”

---

## 3️⃣ Common Causes of Deadlocks

* Inconsistent lock order
* Long-running transactions
* Pessimistic locking (`FOR UPDATE`)
* Multiple tables updated in different orders

---

## 4️⃣ How PostgreSQL Detects Deadlocks

PostgreSQL uses **deadlock detection**, not prevention.

### Detection Mechanism

* Builds a **wait-for graph**
* Periodically checks for cycles
* If cycle found → deadlock detected

---

## 5️⃣ Deadlock Resolution

### What PostgreSQL Does

* Aborts **one transaction** (victim)
* Rolls it back
* Other transaction continues

### Error Example

```text
ERROR: deadlock detected
DETAIL: Process 123 waits for ShareLock on transaction 456; blocked by process 789.
```

🔑 Interview Line:

> “PostgreSQL resolves deadlocks by aborting one transaction.”

---

## 6️⃣ How to Prevent Deadlocks (Very Important 🔥)

### 1️⃣ Lock Resources in Consistent Order

```text
Always lock rows/tables in the same order
```

### 2️⃣ Keep Transactions Short

* Avoid user input inside transactions
* Commit early

### 3️⃣ Use Appropriate Isolation Level

* Avoid unnecessary Serializable

### 4️⃣ Use Optimistic Locking Where Possible

* Reduce blocking

### 5️⃣ Index Foreign Keys

* Prevent table-level locks during FK checks

---

## 7️⃣ Deadlock vs Lock Wait

| Scenario  | Description                            |
| --------- | -------------------------------------- |
| Lock Wait | Transaction waits, eventually proceeds |
| Deadlock  | Circular wait, one txn must be aborted |

---

## 8️⃣ Interview-Ready Deadlock Explanation (Template)

> “A deadlock happens when two transactions lock resources in different orders, creating a circular wait. PostgreSQL detects this using a wait-for graph and resolves it by aborting one transaction.”

---

## 🔥 Interview One-Liners

* “Deadlocks are caused by circular waits.”
* “PostgreSQL detects, not prevents, deadlocks.”
* “One transaction is chosen as a victim and rolled back.”
* “Consistent lock ordering is the best prevention.”

---

📌 **If you can confidently draw this scenario in an interview, you’re already operating at senior backend level.**
