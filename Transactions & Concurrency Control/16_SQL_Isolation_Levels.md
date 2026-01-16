# 16. SQL Isolation Levels (Concurrency & Consistency 🔥)

> 🔑 **Isolation levels determine how transactions see each other's changes — critical for real-world applications.**

Understanding isolation is **essential for senior SQL interviews**.

---

## 1️⃣ Overview

Isolation levels control **visibility of uncommitted changes** and **concurrent behavior**.

| Level            | Dirty Read | Non-Repeatable Read | Phantom Read |
| ---------------- | ---------- | ------------------- | ------------ |
| Read Uncommitted | ❌          | ❌                   | ❌            |
| Read Committed   | ✅          | ❌                   | ❌            |
| Repeatable Read  | ✅          | ✅                   | ❌            |
| Serializable     | ✅          | ✅                   | ✅            |

🔑 Interview Line:

> “Isolation levels trade off concurrency vs strict consistency.”

---

## 2️⃣ Read Uncommitted

### 🔹 Definition

* Lowest isolation
* Can read **uncommitted changes** from other transactions

### Example

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SELECT * FROM accounts;
```

### Behavior

* Dirty reads allowed
* Rarely used in production

🔑 Interview Line:

> “Read Uncommitted is unsafe and almost never used in real systems.”

---

## 3️⃣ Read Committed (Default in PostgreSQL)

### 🔹 Definition

* Can only see **committed data**
* Non-repeatable reads may occur

### Example

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT balance FROM accounts WHERE id = 1;
```

### Behavior

* Two reads in same transaction may return **different values** if another txn commits in between
* Prevents dirty reads

🔑 Interview Line:

> “Read Committed prevents dirty reads but allows non-repeatable reads.”

---

## 4️⃣ Repeatable Read

### 🔹 Definition

* Guarantees **same rows read twice in transaction are identical**
* Phantom reads may still occur in some DBs (PostgreSQL prevents phantoms with MVCC)

### Example

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT * FROM orders WHERE user_id = 101;
-- read again later, same result
```

### Behavior

* Safe for consistent reads
* Still can insert new rows matching filter (phantoms)

🔑 Interview Line:

> “Repeatable Read ensures stable reads for rows already selected.”

---

## 5️⃣ Serializable (Highest Level)

### 🔹 Definition

* Transactions behave as if **executed one after another**
* No dirty, non-repeatable, or phantom reads

### Example

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
SELECT * FROM accounts WHERE id = 1;
```

### Behavior

* Most strict, may cause **serialization errors** (retry needed)
* Safest for critical operations

🔑 Interview Line:

> “Serializable gives full consistency at the cost of concurrency.”

---

## 6️⃣ PostgreSQL & MVCC (Subtle but Critical)

* PostgreSQL implements **MVCC**
* Read Committed / Repeatable Read use **snapshot isolation**
* Serializable prevents phantoms by detecting conflicts and aborting transactions

🔑 Interview Line:

> “PostgreSQL uses MVCC to provide consistent snapshots without heavy locks.”

---

## 7️⃣ Isolation Level Choice (Practical)

| Use Case                   | Recommended Level                |
| -------------------------- | -------------------------------- |
| Banking transfer           | Serializable                     |
| Reporting / analytics      | Read Committed / Repeatable Read |
| High concurrency, low risk | Read Committed                   |
| Legacy / experimental      | Read Uncommitted                 |

---

## 8️⃣ Common Interview Pitfalls

❌ Confusing Repeatable Read with Serializable
❌ Assuming Read Committed guarantees same read twice
❌ Thinking Serializable never causes retry

---

## 🔥 Interview One-Liners

* “Higher isolation = less concurrency.”
* “Read Committed is PostgreSQL default.”
* “Repeatable Read prevents non-repeatable reads but not always phantoms.”
* “Serializable is strict but may abort transactions.”
* “MVCC underpins all snapshot isolation behavior.”

---

📌 **If you can explain isolation levels + MVCC + serialization errors, you’re already operating at senior-level database knowledge.**
