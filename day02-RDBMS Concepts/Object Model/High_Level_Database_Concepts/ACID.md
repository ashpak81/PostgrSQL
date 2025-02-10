ACID
ACID are the four properties of relational database systems that help in making sure that we are able to perform the transactions in a reliable manner. It’s an acronym which refers to the presence of four properties: atomicity, consistency, isolation and durability

ACID, but the good kind
Popular relational databases like MySQL avoid these kinds of data integrity issues by following a few core principles that govern how transactions work. They conform to a transactional standard called ACID. ACID is an acronym for four different words, but it really breaks down into two core principles: completeness and concurrency. First, here’s what ACID stands for:

Atomicity: the “all or nothing” rule — the transaction either happens completely or doesn’t happen at all
Consistency: data is consistent before and after a transaction without any missing steps
Isolation: multiple transactions can happen concurrently without reading the wrong data
Durability: transactional success is robust to system failure
These overlap a lot, so just remember: the point of any ACID compliant DB is to make sure that

Transactions can fail without hurting data integrity
Multiple transactions can occur concurrently without reading and writing the wrong data
ACID is a set of properties, but it’s not a process: how do the SQL databases we use actually achieve ACID compliance? They use a system called locking to keep the database on hold while transactions happen. Locking works like you’d imagine it would: when a transaction begins, the database engine locks the data that it’s working with until the transaction is completed (and sometimes, beyond that). That way, concurrent transactions can’t work with the data that’s being changed by the first transaction.

Once a transaction begins and acquires a lock, it can either finish successfully and commit, or run into an error and abort. It’s sort of like writing in an Excel spreadsheet before saving your work — things are changed, but only softly, until you save them (commit) or revert them (abort). Back to our Amazon example: if our database runs into an error right after updating a user’s order quantity, the transaction will abort, and it’s as if that update never happened. And if the error happened during the update, the credit card will never get charged. Either everything happens or nothing happens, and that’s ACID.

