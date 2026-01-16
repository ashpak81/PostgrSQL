# 07. SQL Common Table Expressions (CTEs)

Common Table Expressions (CTEs) are **temporary named result sets** that exist only for the duration of a query. They improve **readability, maintainability, and correctness** — interviewers love them.

---

## 🔑 What Interviewers Expect You to Know

* Difference between **CTEs and subqueries**
* When CTEs help readability vs when they hurt performance
* Non-recursive vs Recursive CTEs
* Real-world use cases (hierarchy, reports, deduplication)

---

## 1️⃣ Basic Syntax

```sql
WITH cte_name AS (
  SELECT ...
)
SELECT *
FROM cte_name;
```

🔑 **Interview Line**:

> “A CTE is like a temporary, named subquery that makes complex SQL easier to read.”

---

## 2️⃣ Non-Recursive CTEs (Most Common)

### ✅ Use Case 1: Improve Readability

❌ Hard-to-read query:

```sql
SELECT name, salary
FROM employee_salary
WHERE salary > (
  SELECT AVG(salary)
  FROM employee_salary
);
```

✅ With CTE:

```sql
WITH avg_salary AS (
  SELECT AVG(salary) AS avg_sal
  FROM employee_salary
)
SELECT name, salary
FROM employee_salary, avg_salary
WHERE salary > avg_sal;
```

✔ Cleaner logic
✔ Easy to explain in interviews

---

### ✅ Use Case 2: Reuse Derived Data

```sql
WITH dept_avg AS (
  SELECT dept, AVG(salary) AS avg_sal
  FROM employee_salary
  GROUP BY dept
)
SELECT e.name, e.salary, d.avg_sal
FROM employee_salary e
JOIN dept_avg d ON e.dept = d.dept;
```

🔑 **Interview Tip**:

> “CTEs help when the same derived logic is needed multiple times.”

---

## 3️⃣ CTE vs Subquery vs View (Very Important)

| Feature     | CTE       | Subquery  | View      |
| ----------- | --------- | --------- | --------- |
| Readability | ✅ High    | ❌ Low     | ✅ High    |
| Reusable    | ❌ No      | ❌ No      | ✅ Yes     |
| Lifetime    | One query | One query | Permanent |
| Debuggable  | ✅ Yes     | ❌ Hard    | ✅ Yes     |

🔑 **Interview Line**:

> “CTEs improve readability, but they’re not stored or reusable like views.”

---

## 4️⃣ Performance Reality (Senior-Level Truth)

### ⚠️ Important Note

* In **PostgreSQL < 12**, CTEs are optimization fences
* Query planner **must execute CTE fully** before outer query

❌ Can be slower than inline subqueries

### ✅ PostgreSQL 12+

* Planner can inline CTEs
* Performance similar to subqueries

🔑 **Interview Line**:

> “CTEs help readability, but I always validate performance using EXPLAIN.”

---

## 5️⃣ Recursive CTEs (High-Signal Topic 🔥)

Recursive CTEs allow a query to **reference itself** — mainly used for **hierarchical data**.

---

## 6️⃣ Recursive CTE Structure (Must Memorize)

```sql
WITH RECURSIVE cte_name AS (
  -- Anchor query
  SELECT ...

  UNION ALL

  -- Recursive query
  SELECT ...
  FROM cte_name
  JOIN table ON condition
)
SELECT * FROM cte_name;
```

🔑 **Golden Rule**:

> “Anchor query defines the start; recursive query defines how to move.”

---

## 7️⃣ Example: Employee Hierarchy

### `employees`

| emp_id | name      | manager_id |
| ------ | --------- | ---------- |
| 1      | CEO       | NULL       |
| 2      | Manager A | 1          |
| 3      | Manager B | 1          |
| 4      | Dev A     | 2          |
| 5      | Dev B     | 2          |

---

### 🎯 Find all subordinates of CEO

```sql
WITH RECURSIVE emp_tree AS (
  -- Anchor: CEO
  SELECT emp_id, name, manager_id
  FROM employees
  WHERE manager_id IS NULL

  UNION ALL

  -- Recursive
  SELECT e.emp_id, e.name, e.manager_id
  FROM employees e
  JOIN emp_tree t ON e.manager_id = t.emp_id
)
SELECT * FROM emp_tree;
```

✅ Handles any depth
❌ Cannot be solved with simple JOINs

---

## 8️⃣ Avoiding Infinite Loops (Very Important)

### ❌ Problem

* Cyclic relationships cause infinite recursion

### ✅ Solution

```sql
WITH RECURSIVE emp_tree AS (
  SELECT emp_id, name, manager_id, 1 AS level
  FROM employees
  WHERE manager_id IS NULL

  UNION ALL

  SELECT e.emp_id, e.name, e.manager_id, t.level + 1
  FROM employees e
  JOIN emp_tree t ON e.manager_id = t.emp_id
  WHERE t.level < 10
)
SELECT * FROM emp_tree;
```

🔑 **Interview Line**:

> “I always cap recursion depth to prevent infinite loops.”

---

## 9️⃣ Recursive CTE vs Self JOIN

| Feature       | Recursive CTE | Self JOIN     |
| ------------- | ------------- | ------------- |
| Unknown depth | ✅ Yes         | ❌ No          |
| Readability   | ✅ High        | ❌ Low         |
| Performance   | ⚠️ Depends    | ✅ Predictable |

---

## 🔥 Common Mistakes & Alternatives

### ❌ Using CTE just for style

➡️ Inline subquery may be faster

### ❌ Heavy CTE chains

➡️ Break into steps or materialize temporarily

---

## 🔥 Interview One-Liners

* “CTEs improve readability and debugging.”
* “Recursive CTEs are ideal for hierarchical data.”
* “I always validate CTE performance with EXPLAIN.”

---

## ✅ When to Use CTEs

✔ Complex readable queries
✔ Reused derived datasets
✔ Hierarchical traversal
✔ Reporting logic

---

📌 **If you can explain recursive CTEs confidently, you’re ahead of ~90% candidates.**
