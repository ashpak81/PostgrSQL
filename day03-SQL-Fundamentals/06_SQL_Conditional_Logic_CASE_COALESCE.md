# 06. SQL Conditional Logic (CASE WHEN & COALESCE)

Conditional logic in SQL is used to **apply business rules inside queries**. Interviewers use this topic to test **real-world thinking**, not syntax.

---

## 1️⃣ CASE WHEN – The Most Important Conditional Tool

### 🔹 What is CASE?

`CASE` allows **if–else logic inside SQL queries**.

```sql
CASE
  WHEN condition THEN result
  WHEN condition THEN result
  ELSE result
END
```

🔑 **Interview Line**:

> “CASE lets us implement business rules directly in SQL queries.”

---

## 2️⃣ Simple Example (Beginner → Clear)

### 🎯 Categorize employees by salary

```sql
SELECT
  name,
  salary,
  CASE
    WHEN salary >= 70000 THEN 'High'
    WHEN salary >= 50000 THEN 'Medium'
    ELSE 'Low'
  END AS salary_category
FROM employee_salary;
```

| salary | category |
| ------ | -------- |
| 70000  | High     |
| 60000  | Medium   |
| 40000  | Low      |

---

## 3️⃣ CASE in WHERE Clause (Very Common Interview Trap)

### ❌ Bad / Non-performant

```sql
WHERE CASE WHEN status = 'ACTIVE' THEN 1 ELSE 0 END = 1
```

❌ Breaks index usage

### ✅ Better Alternative

```sql
WHERE status = 'ACTIVE'
```

🔑 **Rule**:

> “Avoid CASE in WHERE when simple boolean logic works.”

---

## 4️⃣ CASE with Aggregation (High-Value Interview Pattern)

### 🎯 Count active vs inactive users

```sql
SELECT
  COUNT(CASE WHEN status = 'ACTIVE' THEN 1 END) AS active_users,
  COUNT(CASE WHEN status = 'INACTIVE' THEN 1 END) AS inactive_users
FROM users;
```

✅ No subqueries needed

---

## 5️⃣ CASE vs FILTER (PostgreSQL – Senior Tip)

### ❌ Traditional CASE

```sql
COUNT(CASE WHEN status = 'ACTIVE' THEN 1 END)
```

### ✅ Better (PostgreSQL)

```sql
COUNT(*) FILTER (WHERE status = 'ACTIVE')
```

🔑 **Interview Line**:

> “In PostgreSQL, FILTER is cleaner and often more readable than CASE for conditional aggregation.”

---

## 6️⃣ Nested CASE (Use Carefully)

```sql
CASE
  WHEN dept = 'IT' THEN
    CASE WHEN salary > 70000 THEN 'Senior IT' ELSE 'Junior IT' END
  WHEN dept = 'HR' THEN 'HR'
  ELSE 'Other'
END
```

⚠️ Too much nesting = unreadable SQL

---

## 7️⃣ COALESCE – NULL Handling (Extremely Important)

### 🔹 What is COALESCE?

Returns **first non-NULL value**.

```sql
COALESCE(value1, value2, value3)
```

🔑 **Interview Line**:

> “COALESCE is used to handle NULLs safely in calculations and comparisons.”

---

## 8️⃣ COALESCE Basic Example

### 🎯 Replace NULL bonus with 0

```sql
SELECT
  name,
  salary + COALESCE(bonus, 0) AS total_salary
FROM employee_salary;
```

❌ Without COALESCE → result becomes NULL

---

## 9️⃣ COALESCE vs ISNULL / NVL

| Function | DB                 |
| -------- | ------------------ |
| COALESCE | ANSI SQL (All DBs) |
| ISNULL   | SQL Server         |
| NVL      | Oracle             |

✅ Prefer `COALESCE` for portability

---

## 🔟 COALESCE in JOIN Conditions (Real Bug Fix)

### ❌ Problematic JOIN

```sql
ON orders.discount = promotions.discount
```

### ✅ Safer JOIN

```sql
ON COALESCE(orders.discount, 0) = COALESCE(promotions.discount, 0)
```

⚠️ May impact index usage → use carefully

---

## 1️⃣1️⃣ COALESCE vs CASE

### ❌ Overkill CASE

```sql
CASE WHEN bonus IS NULL THEN 0 ELSE bonus END
```

### ✅ Cleaner COALESCE

```sql
COALESCE(bonus, 0)
```

🔑 **Rule**:

> “Use COALESCE for NULL handling, CASE for conditional branching.”

---

## 1️⃣2️⃣ Common Mistakes & Alternatives

### ❌ COALESCE in WHERE on indexed column

```sql
WHERE COALESCE(status, 'X') = 'ACTIVE'
```

❌ Index not used

### ✅ Better

```sql
WHERE status = 'ACTIVE'
```

---

## 🔥 Interview One-Liners

* “CASE implements business rules; COALESCE handles NULLs.”
* “I avoid CASE in WHERE to keep indexes usable.”
* “COALESCE prevents NULL from breaking arithmetic expressions.”

---

## ✅ When to Use

✔ CASE → Business logic, conditional aggregation
✔ COALESCE → Default values, NULL-safe calculations

---

📌 **Strong CASE + COALESCE usage signals production-level SQL skills.**
