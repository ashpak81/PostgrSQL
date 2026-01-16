# SQL Interview & Practical Knowledge Roadmap

> **Target**: Backend / Database Developer interviews (Mid → Senior)
>
> Focused on **real-world usage, performance, and design trade-offs**, not just syntax.

---

## 1. SQL Fundamentals (Must-Have)

### Core Queries

* `SELECT`, `WHERE`, `ORDER BY`
* `LIMIT`, `OFFSET`
* `DISTINCT`

### Joins

* `INNER JOIN`
* `LEFT JOIN`
* `RIGHT JOIN`
* `FULL JOIN`
* Self Join

### Aggregation

* `GROUP BY`
* `HAVING`
* `COUNT(*)` vs `COUNT(column)`

### Conditional Logic

* `CASE WHEN`
* `COALESCE`

### Subqueries

* Correlated subqueries
* Non-correlated subqueries
* `EXISTS` vs `IN`

### Window Functions

* Ranking

  * `ROW_NUMBER()`
  * `RANK()`
  * `DENSE_RANK()`
* Offset functions

  * `LEAD()`
  * `LAG()`
* Aggregates over window

  * `SUM() OVER()`
  * `COUNT() OVER()`

### Common Table Expressions (CTEs)

* Non-recursive CTEs
* Recursive CTEs

---

## 2. Indexing & Query Performance (Very Important)

### Index Fundamentals

* What is an index
* B-tree index (default)
* Hash index (PostgreSQL)

### Composite Indexes

* Index column order importance
* Left-most prefix rule

### Covering Indexes

* `INCLUDE` columns (PostgreSQL)
* Index-only scans

### When Index Is NOT Used

* Low selectivity
* Functions on indexed columns
* Data type mismatch
* `LIKE '%value'`

### Execution Plans

* `EXPLAIN`
* `EXPLAIN ANALYZE`
* Seq Scan vs Index Scan
* Bitmap Index Scan

### Query Optimization

* Reducing full table scans
* Query refactoring
* Join order impact
* Cardinality & selectivity

---

## 3. Transactions & Concurrency Control

### ACID Properties

* Atomicity
* Consistency
* Isolation
* Durability

### Isolation Levels

* Read Uncommitted
* Read Committed
* Repeatable Read
* Serializable

### Concurrency Issues

* Dirty reads
* Non-repeatable reads
* Phantom reads
* Lost updates

### Locking

* Optimistic locking
* Pessimistic locking
* `SELECT ... FOR UPDATE`

### Deadlocks

* How deadlocks occur
* Detection and resolution

### MVCC (PostgreSQL)

* Snapshot isolation
* Visibility rules
* VACUUM & autovacuum

---

## 4. Database Design & Normalization

### Normal Forms

* First Normal Form (1NF)
* Second Normal Form (2NF)
* Third Normal Form (3NF)

### Practical Design Decisions

* Over-normalization problems
* Denormalization for performance
* OLTP vs OLAP design
* Read-heavy vs write-heavy systems

### Schema Patterns

* Star schema (basic idea)
* Lookup / reference tables

---

## 5. Pagination Strategies

### Offset-based Pagination

* How it works
* Performance issues with large offsets
* Inconsistent results on updates

### Keyset (Cursor-based) Pagination

* How it works
* Why it is faster
* Limitations (cannot jump to page N)

---

## 6. Constraints & Data Integrity

### Constraints

* Primary Key
* Foreign Key
* Unique
* Check constraints

### Cascading Actions

* `ON DELETE CASCADE`
* `ON UPDATE CASCADE`

### Practical Considerations

* FK performance impact
* Deferred constraints
* Why some microservices avoid FKs

---

## 7. Stored Procedures vs Functions

### Differences

* Purpose and usage
* Return values
* Transaction control

### Design Considerations

* When DB-side logic makes sense
* When to move logic to service layer
* Maintainability concerns

---

## 8. Real-World Database Scenarios

### Large Data Handling

* Handling large tables
* Read replicas (conceptual)
* Replication lag awareness

### Data Lifecycle

* Soft delete vs hard delete
* Archival strategies
* Audit tables

### Scalability

* Table partitioning

  * Range
  * List
  * Hash
* Sharding (conceptual)

  * Horizontal sharding
  * Challenges & trade-offs

### Distributed Systems Concepts

* Idempotency
* Schema migrations
* Backward compatibility
* Data consistency across microservices

---

## 9. SQL Interview Gotchas (High-Value)

* `NOT IN` with `NULL`
* `LEFT JOIN` + `WHERE` condition mistakes
* `UNION` vs `UNION ALL`
* `DELETE` vs `TRUNCATE`
* `WHERE` vs `HAVING`
* `COUNT(*)` vs `COUNT(column)`

---

## Interview Advantage Tips (Senior-Level Signals)

🔑 **If you can draw a deadlock scenario using two transactions, you’re ahead of 90% of candidates.**

🔑 **If you can explain why an index is not used even when it exists, you signal real production experience.**

🔑 **If you can read an `EXPLAIN ANALYZE` plan and explain where time is spent, interviewers trust your optimization skills.**

🔑 **If you can justify when NOT to add an index, you stand out as a mature engineer.**

🔑 **If you can explain `EXISTS` vs `IN` with performance reasoning, you show depth beyond syntax.**

🔑 **If you can explain why OFFSET pagination breaks at scale and propose keyset pagination, you show backend-system thinking.**

🔑 **If you can discuss trade-offs of Foreign Keys in microservices, you show architectural awareness.**

🔑 **If you can explain MVCC without using textbook definitions, you sound senior immediately.**

---

## Final Preparation Tips

* Always explain **WHY**, not just **HOW**
* Be ready to discuss **trade-offs and failure cases**
* Practice reading execution plans daily
* Relate answers to **real production incidents**
* Think like a system designer, not just a query writer
