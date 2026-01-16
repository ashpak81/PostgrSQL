# 05. SQL Window Functions (Interview-Critical)

> 🔑 **Golden Rule**: `GROUP BY` reduces rows, **Window Functions preserve rows**.

Window functions allow calculations across a *logical window of rows* related to the current row **without collapsing result rows**.

---

## 1️⃣ Mental Model (Very Important)

* `GROUP BY` → Aggregates rows → **fewer rows**
* Window Function → Aggregates over a window → **same number of rows**

🔑 **Interview Line**:

> “Window functions let us do aggregation, ranking, and comparisons while keeping row-level detail intact.”

---

## 2️⃣ Sample Table (Used in All Examples)

```text
employee_salary
-------------------------------------------
emp_id | name   | dept | salary | joining_date
-------------------------------------------
1      | Asha   | IT   | 60000  | 2022-01-10
2      | Ravi   | IT   | 70000  | 2021-03-15
3      | Neha   | HR   | 50000  | 2022-07-01
4      | Amit   | HR   | 50000  | 2021-01-20
5      | Karan  | IT   | 70000  | 2023-02-10
```

---

## 3️⃣ ROW_NUMBER vs RANK vs DENSE_RANK

### 🎯 Use Case

Rank employees by salary **within each department**.

---

### 🔹 ROW_NUMBER()

* Always unique
* No ties

```sql
ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC)
```

| salary | row_number |
| ------ | ---------- |
| 70000  | 1          |
| 70000  | 2          |
| 60000  | 3          |

✅ Best for **pagination** and **deduplication**

---

### 🔹 RANK()

* Same rank for ties
* **Skips numbers**

```sql
RANK() OVER (PARTITION BY dept ORDER BY salary DESC)
```

| salary | rank |
| ------ | ---- |
| 70000  | 1    |
| 70000  | 1    |
| 60000  | 3    |

---

### 🔹 DENSE_RANK()

* Same rank for ties
* **No gaps**

```sql
DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC)
```

| salary | dense_rank |
| ------ | ---------- |
| 70000  | 1          |
| 70000  | 1          |
| 60000  | 2          |

🔑 **Interview Tip**:

> “Use `ROW_NUMBER` when you need one unique row, `DENSE_RANK` for Top-N queries with ties.”

---

## 4️⃣ Top-N Per Group (Very Common Interview Question)

### 🎯 Find **Top 2 highest paid employees per department**

```sql
SELECT *
FROM (
  SELECT *,
         DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS rnk
  FROM employee_salary
) t
WHERE rnk <= 2;
```

❌ `LIMIT` cannot solve per-group ranking

---

## 5️⃣ LEAD & LAG (Time-Based Analysis)

### 🎯 Compare salary with **previous employee** in same department

```sql
SELECT
  name,
  dept,
  salary,
  LAG(salary) OVER (PARTITION BY dept ORDER BY joining_date) AS prev_salary
FROM employee_salary;
```

| name | salary | prev_salary |
| ---- | ------ | ----------- |
| Ravi | 70000  | NULL        |
| Asha | 60000  | 70000       |

✅ Used in:

* Salary change analysis
* Sales comparison
* Stock price movement

---

## 6️⃣ Running Totals (Business-Critical)

### 🎯 Running salary total per department

```sql
SELECT
  name,
  dept,
  salary,
  SUM(salary) OVER (
    PARTITION BY dept
    ORDER BY joining_date
  ) AS running_total
FROM employee_salary;
```

✅ No subquery
✅ No GROUP BY

---

## 7️⃣ Window Frame (Advanced Concept)

### ❓ What rows participate in calculation?

```sql
SUM(salary) OVER (
  PARTITION BY dept
  ORDER BY joining_date
  ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```

🔑 **Interview Line**:

> “Window frame defines how many rows before and after the current row are included in the calculation.”

---

## 8️⃣ Common Mistakes & Correct Alternatives

### ❌ Filtering directly on window function

```sql
WHERE ROW_NUMBER() OVER (...) = 1
```

🚫 Not allowed

### ✅ Correct approach

```sql
SELECT *
FROM (
  SELECT *, ROW_NUMBER() OVER (...) AS rn
  FROM table
) t
WHERE rn = 1;
```

---

## 9️⃣ Performance Notes

* Window functions execute **after WHERE, GROUP BY**
* Index helps on:

  * `PARTITION BY`
  * `ORDER BY`
* Avoid unnecessary window functions on large datasets

---

## 🔥 Interview One-Liners

* “Window functions preserve row-level data while enabling advanced analytics.”
* “I use `ROW_NUMBER` for pagination and `DENSE_RANK` for Top-N per group.”
* “LEAD and LAG are ideal for time-series comparisons.”

---

## ✅ When to Use Window Functions

✔ Ranking problems
✔ Running totals
✔ De-duplication
✔ Time-based comparisons
✔ Analytical queries

---

📌 **If you understand window functions well, you’re ahead of ~80% SQL candidates.**
