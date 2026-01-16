# 19. MVCC in PostgreSQL (Snapshot Isolation & Visibility 🔥)

> 🔑 **MVCC is the heart of PostgreSQL concurrency. If you understand this, isolation levels, locking, and VACUUM all make sense.**

---

## 1️⃣ What Is MVCC?

**MVCC (Multi-Version Concurrency Control)** allows:

* Readers **not to block writers**
* Writers **not to block readers**

PostgreSQL achieves this by **keeping multiple versions of a row**, not by locking reads.

🔑 Interview Line:

> “PostgreSQL uses MVCC to provide high concurrency without read locks.”

---

## 2️⃣ Row Versions (How PostgreSQL Stores Data)

Every row has hidden system columns:

* `xmin` → transaction ID that **created** the row
* `xmax` → transaction ID that **deleted/updated** the row

### Example

```text
Row version 1: xmin=100, xmax=NULL
Row version 2: xmin=105, xmax=NULL
```

Old versions are kept until no transaction needs them.

---

## 3️⃣ Snapshot Isolation

### 🔹 What Is a Snapshot?

A snapshot is a **view of committed transactions at a point in time**.

* Assigned at query start (Read Committed)
* Assigned at transaction start (Repeatable Read)

---

### Example (Read Committed)

**Transaction A**

```sql
BEGIN;
SELECT balance FROM accounts; -- snapshot S1
```

**Transaction B**

```sql
UPDATE accounts SET balance = 2000;
COMMIT;
```

**Transaction A**

```sql
SELECT balance FROM accounts; -- snapshot S2 (sees update)
```

---

## 4️⃣ Visibility Rules (Very Important 🔥)

A row is **visible** to a transaction if:

* `xmin` transaction is committed
* `xmax` is either NULL or from an uncommitted transaction
* Row version matches transaction snapshot

### Simplified Rule

```text
Visible if:
- created before snapshot
- not deleted before snapshot
```

🔑 Interview Line:

> “Visibility is decided using xmin/xmax and the transaction snapshot.”

---

## 5️⃣ Isolation Levels & MVCC

| Isolation Level | Snapshot Behavior             |
| --------------- | ----------------------------- |
| Read Committed  | New snapshot per query        |
| Repeatable Read | One snapshot per transaction  |
| Serializable    | Snapshot + conflict detection |

PostgreSQL **prevents dirty reads** by design.

---

## 6️⃣ UPDATE = DELETE + INSERT

In PostgreSQL:

```sql
UPDATE accounts SET balance = 1500 WHERE id = 1;
```

Internally becomes:

* Old row → `xmax = txn_id`
* New row → `xmin = txn_id`

Old version remains until vacuumed.

---

## 7️⃣ Why VACUUM Is Required

Because MVCC keeps old row versions:

* Tables grow (bloat)
* Performance degrades

VACUUM:

* Removes dead row versions
* Marks space reusable
* Updates visibility map

🔑 Interview Line:

> “VACUUM cleans dead tuples created by MVCC.”

---

## 8️⃣ Autovacuum

### 🔹 What It Is

* Background process
* Automatically runs VACUUM & ANALYZE

### What It Does

* Prevents table bloat
* Avoids transaction ID wraparound
* Keeps planner statistics fresh

---

### Danger Without Autovacuum

❌ Table bloat
❌ Slow queries
❌ Transaction ID wraparound → forced shutdown

🔑 Interview Line:

> “Autovacuum is critical for PostgreSQL health.”

---

## 9️⃣ VACUUM vs VACUUM FULL

| Command     | Behavior                                      |
| ----------- | --------------------------------------------- |
| VACUUM      | Non-blocking, reclaims space internally       |
| VACUUM FULL | Blocking, rewrites table, returns space to OS |

---

## 🔥 Common Interview Traps

❌ MVCC uses undo logs like Oracle
❌ UPDATE modifies rows in place
❌ VACUUM is optional

✅ Correct:

* PostgreSQL keeps multiple row versions
* UPDATE creates new row version
* VACUUM is mandatory

---

## 🔥 Interview One-Liners

* “MVCC allows readers and writers to work without blocking.”
* “Visibility is controlled by transaction snapshots.”
* “UPDATE is implemented as delete + insert.”
* “VACUUM is essential to clean dead tuples.”
* “Autovacuum prevents table bloat and XID wraparound.”

---

📌 **If you can explain MVCC + visibility rules + VACUUM clearly, you are 100% senior-level in PostgreSQL interviews.**
