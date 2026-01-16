# 17. SQL Concurrency Issues (Classic Interview Traps ⚠️)

> 🔑 **If you can explain concurrency issues with examples, interviewers know you truly understand transactions.**

Concurrency issues occur when **multiple transactions run at the same time** without proper isolation or locking.

---

## 1️⃣ Dirty Reads

### 🔹 What Is a Dirty Read?

A transaction reads **uncommitted data** from another transaction.

---

### Example

**Transaction A**

```sql
BEGIN;
UPDATE accounts SET balance = 500 WHERE id = 1;
-- not committed yet
```

**Transaction B**

```sql
SELECT balance FROM accounts WHERE id = 1; -- sees 500 (dirty)
```

**Transaction A**

```sql
ROLLBACK;
```

❌ Transaction B read invalid data

---

### Prevention

* Isolation level ≥ **Read Committed**

🔑 Interview Line:

> “Dirty reads occur when uncommitted data is visible.”

---

## 2️⃣ Non-Repeatable Reads

### 🔹 What Is a Non-Repeatable Read?

The same row returns **different values** within the same transaction.

---

### Example

**Transaction A**

```sql
BEGIN;
SELECT balance FROM accounts WHERE id = 1; -- 1000
```

**Transaction B**

```sql
UPDATE accounts SET balance = 1500 WHERE id = 1;
COMMIT;
```

**Transaction A**

```sql
SELECT balance FROM accounts WHERE id = 1; -- 1500
```

❌ Same query, different result

---

### Prevention

* Isolation level ≥ **Repeatable Read**

🔑 Interview Line:

> “Non-repeatable reads happen when committed updates change previously read data.”

---

## 3️⃣ Phantom Reads

### 🔹 What Is a Phantom Read?

New rows **appear or disappear** when the same query is re-executed.

---

### Example

**Transaction A**

```sql
BEGIN;
SELECT COUNT(*) FROM orders WHERE amount > 1000; -- 5
```

**Transaction B**

```sql
INSERT INTO orders(amount) VALUES (2000);
COMMIT;
```

**Transaction A**

```sql
SELECT COUNT(*) FROM orders WHERE amount > 1000; -- 6
```

❌ Phantom row appeared

---

### Prevention

* **Serializable** isolation
* PostgreSQL Repeatable Read prevents phantoms using MVCC

🔑 Interview Line:

> “Phantom reads involve new rows matching a previous condition.”

---

## 4️⃣ Lost Updates (Very Important 🔥)

### 🔹 What Is a Lost Update?

Two transactions update the same row, and **one update overwrites the other**.

---

### Example

**Transaction A**

```sql
BEGIN;
SELECT balance FROM accounts WHERE id = 1; -- 1000
```

**Transaction B**

```sql
BEGIN;
SELECT balance FROM accounts WHERE id = 1; -- 1000
UPDATE accounts SET balance = 1200 WHERE id = 1;
COMMIT;
```

**Transaction A**

```sql
UPDATE accounts SET balance = 1100 WHERE id = 1;
COMMIT;
```

❌ Update by Transaction B is lost

---

### Prevention

* `SELECT ... FOR UPDATE`
* Optimistic locking (version column)
* Serializable isolation

🔑 Interview Line:

> “Lost updates occur when concurrent writes overwrite each other.”

---

## 5️⃣ Concurrency Issues vs Isolation Levels

| Issue               | Read Uncommitted | Read Committed | Repeatable Read | Serializable |
| ------------------- | ---------------- | -------------- | --------------- | ------------ |
| Dirty Read          | ❌                | ✅              | ✅               | ✅            |
| Non-repeatable Read | ❌                | ❌              | ✅               | ✅            |
| Phantom Read        | ❌                | ❌              | ❌               | ✅            |
| Lost Update         | ❌                | ❌              | ❌               | ✅            |

---

## 6️⃣ How PostgreSQL Handles This

* Uses **MVCC**
* Prevents dirty reads by default
* Detects conflicts at higher isolation levels
* Aborts one transaction if needed

🔑 Interview Line:

> “PostgreSQL prevents dirty reads using MVCC.”

---

## 🔥 Interview One-Liners

* “Dirty reads expose uncommitted data.”
* “Non-repeatable reads change existing rows.”
* “Phantom reads introduce new rows.”
* “Lost updates overwrite concurrent changes.”
* “Isolation levels exist to control these anomalies.”

---

📌 **If you can explain all four anomalies with examples, you’re already in the top tier of SQL candidates.**
