What are Relational Databases?

Relational databases are a type of database management system (DBMS) that stores and organizes data in a structured format called tables.
These tables are made up of rows, also known as records or tuples, and columns, which are also called attributes or fields. 
The term “relational” comes from the fact that these tables can be related to one another through keys and relationships.

Relational databases are also typically associated with transactional databases, which execute commands, or transactions, collectively. A popular example that is used to illustrate this is a bank transfer. A defined amount is withdrawn from one account, and then it is deposited within another. The total amount of money is withdrawn and deposited, and this transaction cannot occur in any kind of partial sense. Transactions have specific properties. Represented by the acronym, ACID, ACID properties are defined as:

Atomicity: All changes to data are performed as if they are a single operation. That is, all the changes are performed, or none of them are.
Consistency: Data remains in a consistent state from state to finish, reinforcing data integrity.
Isolation: The intermediate state of a transaction is not visible to other transactions, and as a result, transactions that run concurrently appear to be serialized.
Durability: After the successful completion of a transaction, changes to data persist and are not undone, even in the event of a system failure.

Relational database vs. relational database management system

While a relational database organizes data based off a relational data model, a relational database management system (RDBMS) is a more specific reference to the underlying database software that enables users to maintain it.
These programs allow users to create, update, insert, or delete data in the system, and they provide:
Data structure
Multi-user access
Privilege control
Network access
Examples of popular RDBMS systems include MySQL, PostgreSQL, and IBM DB2. Additionally, a relational database system differs from a basic database management system (DBMS) in that it stores data in tables while a DBMS stores information as files.

