Key differences: PostgreSQL vs MySQL
While PostgreSQL and MySQL are conceptually similar, there are many differences to consider before implementing them.

ACID compliance
Atomicity, consistency, isolation, and durability (ACID) are database properties that ensure a database remains in a valid state even after unexpected errors. For example, if you update a large number of rows but the system fails midway, no row should be modified.

MySQL offers ACID compliance only when you use it with InnoDB and NDB Cluster storage engines or software modules. PostgreSQL is fully ACID compliant in all configurations.

Concurrency control
Multiversion concurrency control (MVCC) is an advanced database feature that creates duplicate copies of records to safely read and update the same data in parallel. When you use MVCC, multiple users can read and modify the same data simultaneously without compromising data integrity.

MVCC varies by Storage Engine in MySQL. For example, MVCC is fully supported when you use the InnoDB storage engine. MVCC is not supported in the MyISAM storage engine. PostgreSQL supports MVCC in all configurations.

Indexes
Databases use indexes to retrieve data faster. You can index frequently accessed data by configuring the database management system to sort and store it differently from the other data.

MySQL supports B-tree and R-tree indexing that stores hierarchically indexed data. PostgreSQL index types include trees, expression indexes, partial indexes, and hash indexes. There are more options to fine-tune your database performance requirements as you scale.

Data types
MySQL is a purely relational database. PostgreSQL, on the other hand, is an object-relational database. This means that in PostgreSQL, you can store data as objects with properties. Objects are a common data type in many programming languages like Java and .NET. Objects support paradigms like parent-child relationships and inheritance.

Working with PostgreSQL is more intuitive for database developers. PostgreSQL also supports other additional data types like arrays and XML.

Views
A view is a data subset that the database system creates by pulling relevant data from multiple tables.

While MySQL supports views, PostgreSQL offers advanced view options. For example, you can precompute some values in advance (like the total value of all orders over a given period) to create materialized views. Materialized views improve database performance for complicated queries.

Stored procedures
Stored procedures are structured query language (SQL) queries or code statements you can write and save in advance. You can reuse the same code repeatedly, which makes database management tasks more efficient.

While both MySQL and PostgreSQL support stored procedures, PostgreSQL allows you to call stored procedures written in languages other than SQL.

Triggers
A trigger is a stored procedure that runs automatically when a related event occurs in the database management system.

In a MySQL database, you can only use AFTER and BEFORE triggers for SQL INSERT, UPDATE, and DELETE statements. That means the procedure will run automatically before or after the user modifies the data. In contrast, PostgreSQL supports the INSTEAD OF trigger, so you can run complex SQL statements using functions.
