# 08. SQL Index Fundamentals (Performance Cornerstone)

Indexes are **the most important topic in SQL interviews** for backend roles. A good answer here immediately signals **production experience**.

---

## 🔑 What Interviewers Really Test

* Do you know **what an index actually is** (not just definition)?
* Do you understand **B-tree traversal**?
* Can you explain **when indexes help vs hurt**?
* Do you know **PostgreSQL index types** and trade-offs?

---

## 1️⃣ What Is an Index?

### 📘 Definition (Simple)

An index is a **data structure that allows the database to find rows faster** without scanning the entire table.

### 🧠 Mental Model

* Table → **unsorted heap of rows**
* Index → **sorted lookup structure** pointing to table rows

🔑 **Interview Line**:

> “An index trades extra storage and write cost for faster reads.”

---

## 2️⃣ What Problem Does an Index Solve?

### ❌ Without Index (Full Table Scan)

```sql
SELECT * FROM users WHERE email = 'abc@gmail.com';
```

* Database checks **every row**
* O(N) complexity

### ✅ With Index on `email`

* Database jumps directly to matching rows
* O(log N) complexity

---

## 3️⃣ B-Tree Index (Default & Most Important)

### 🔹 What Is a B-Tree Index?

* Balanced tree data structure
* Keeps data **sorted**
* Ensures **logarithmic search time**

PostgreSQL uses **B+Tree** internally.

---

## 4️⃣ How B-Tree Index Works (Must Understand)

1. Root node → points to intermediate nodes
2. Intermediate nodes → narrow the search range
3. Leaf nodes → contain **index key + row pointer**

🔑 **Interview Line**:

> “In a B-tree, all actual data pointers live in leaf nodes, keeping the tree balanced.”

---

## 5️⃣ What Queries Use B-Tree Indexes?

### ✅ Supported Operations

* `=` equality
* `<`, `>`, `<=`, `>=`
* `BETWEEN`
* `LIKE 'abc%'`
* `ORDER BY`
* `MIN()` / `MAX()`

### ❌ Not Efficient For

* `LIKE '%abc'`
* Functions on indexed columns

---

## 6️⃣ Example: Creating a B-Tree Index

```sql
CREATE INDEX idx_users_email
ON users(email);
```

PostgreSQL automatically uses **B-tree** unless specified.

---

## 7️⃣ Hash Index (PostgreSQL)

### 🔹 What Is a Hash Index?

* Uses hash function
* Optimized for **equality (`=`) lookups only**
* No ordering

```sql
CREATE INDEX idx_users_email_hash
ON users USING HASH (email);
```

---

## 8️⃣ B-Tree vs Hash Index (Critical Comparison)

| Feature        | B-Tree | Hash       |
| -------------- | ------ | ---------- |
| Equality (`=`) | ✅      | ✅ (faster) |
| Range queries  | ✅      | ❌          |
| ORDER BY       | ✅      | ❌          |
| MIN / MAX      | ✅      | ❌          |
| Default        | ✅      | ❌          |
| WAL-safe       | ✅      | ✅ (PG 10+) |

🔑 **Interview Line**:

> “B-tree is the default because it supports both equality and range queries.”

---

## 9️⃣ Why Hash Index Is Rarely Used

* Cannot support range queries
* Cannot be used for sorting
* Less flexible than B-tree

✅ Hash index only makes sense when:

* Query uses **only `=`**
* Table is very large
* No sorting or range needed

---

## 🔟 Index Usage & Query Planner

PostgreSQL decides to use an index based on:

* Selectivity
* Table size
* Cost estimation

🔑 **Interview Line**:

> “Indexes are not forced; the query planner decides based on cost.”

---

## 1️⃣1️⃣ Common Index Pitfalls & Alternatives

### ❌ Function on indexed column

```sql
WHERE LOWER(email) = 'abc@gmail.com'
```

❌ Index not used

### ✅ Alternative

```sql
WHERE email = 'abc@gmail.com'
```

Or create functional index:

```sql
CREATE INDEX idx_users_lower_email ON users (LOWER(email));
```

---

## 1️⃣2️⃣ When Indexes Hurt Performance

* Heavy INSERT / UPDATE / DELETE
* Low-selectivity columns (e.g. gender)
* Too many indexes on one table

🔑 **Interview Line**:

> “Indexes speed up reads but slow down writes.”

---

## 🔥 Interview One-Liners

* “Indexes reduce full table scans.”
* “B-tree is the default due to its versatility.”
* “Hash indexes are limited to equality lookups.”
* “The query planner decides index usage, not the developer.”

---

## ✅ When to Use Which Index

✔ B-Tree → Almost always
✔ Hash → Rare, equality-only cases

---

📌 **Strong index fundamentals instantly place you in the top backend candidates.**
