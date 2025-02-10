Multi-Version Concurrency Control (MVCC)
Multi-Version Concurrency Control (MVCC) is a technique used by PostgreSQL to allow multiple transactions to access the same data concurrently without conflicts or delays. It ensures that each transaction has a consistent snapshot of the database and can operate on its own version of the data.

What is MVCC? (multiversion concurrency control)
Multiversion concurrency control (MVCC) is a database optimization technique. MVCC creates duplicate copies of records so that data can be safely read and updated at the same time.
With MVCC, database reads and writes don’t block each other, and that greatly enhances the user experience.

What are the benefits of MVCC?
When implemented properly by a DBMS, multiversion concurrency control provides the following benefits:

Improved read access performance;
Diminished need for database locks;
Fewer database access contention issues;
Continued record isolation for write operations; and
Reduced number of database deadlocks.

How does an MVCC database work?
While DBMS vendors are free to implement MVCC in their own ways, multiversion concurrency control usually works like this:

Every database record has a version number.
Concurrent reads happen against the record with the highest version number.
Write operations operate on copy of the record, not the record itself.
Users continue to read the older version while the copy is updated.
After the write operation is successful, the version id is incremented.
Subsequent concurrent reads use the updated version.
When a new update occurs, a new version is again created, continuing the cycle.

What is the difference between MVCC vs locking?
Unlike traditional a DMBS, multiversion concurrency control does not lock a record when a write operation is about to occur. Instead, a new version of the record, with an incremented version number, is created.

Users can continue to read the old version of the record while the new record is transitionally edited and updated. This eliminates the need for locks, as well as contention and deadlock issues.

When the new version of the record is committed to the database, all future read operations work on the updated version. New write operations again create a new version, and the cycle continues.

What are the drawbacks to multiversion concurrency control (MVCC)?
While MVCC offers many benefits, there are two key drawbacks to multiversion concurrency control:

Concurrent update control methods are difficult to implement.
The database grows in size and becomes bloated by multiple versions of DBMS records.
For the user, or even the developer, the complexity involved to implement MVCC concurrency control methods is completely hidden, as the functionality is provided by the database vendor.

The developer can write SQL, and the end user can consume applications as they normally would. The fact that the DBMS uses multiversion concurrency control behind the scenes is completely transparent to them.

PostgreSQL MVCC multiversion concurrency control
Every vendor has its own strategy to minimize the main drawback of MVCC: the ever-expanding size of the database, given the potential number of record versions created.

To deal with version bloat, a PostgreSQL MVCC database uses a cleverly named process, VACCUM, to identify and delete duplicate and unneeded records created by the multiversion concurrency control process.

