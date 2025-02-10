  Databases in PostgreSQL
In PostgreSQL, a database is a named collection of tables, indexes, views, stored procedures, and other database objects. Each PostgreSQL server can manage multiple databases, enabling the separation and organization of data sets for various applications, projects, or users.

Tables in PostgreSQL
A table is one of the primary data storage objects in PostgreSQL. In simple terms, a table is a collection of rows or records, organized into columns. Each column has a unique name and contains data of a specific data type.

Schemas
Schemas are an essential part of PostgreSQL’s object model, and they help provide structure, organization, and namespacing for your database objects. A schema is a collection of database objects, such as tables, views, indexes, and functions, that are organized within a specific namespace.

Rows in PostgreSQL
A row in PostgreSQL represents a single, uniquely identifiable record with a specific set of fields in a table. Each row in a table is made up of one or more columns, where each column can store a specific type of data (e.g., integer, character, date, etc.). The structure of a table determines the schema of its rows, and each row in a table must adhere to this schema.

Columns in PostgreSQL
Columns are a fundamental component of PostgreSQL’s object model. They are used to store the actual data within a table and define their attributes such as data type, constraints, and other properties.

Data Types in PostgreSQL
PostgreSQL offers a rich and diverse set of data types, catering to a wide range of applications and ensuring data integrity and performance. These include standard numeric types such as integers, floating-point numbers, and serial types for auto-incrementing fields. Character types like VARCHAR and TEXT handle varying lengths of text, while DATE, TIME, and TIMESTAMP support a variety of temporal data requirements. PostgreSQL also supports a comprehensive set of Boolean, enumerated (ENUM), and composite types, enabling more complex data structures. Additionally, it excels with its support for JSON and JSONB data types, allowing for efficient storage and querying of semi-structured data. The inclusion of array types, geometric data types, and the PostGIS extension for geographic data further extends PostgreSQL’s versatility, making it a powerful tool for a broad spectrum of data management needs.

Queries in PostgreSQL
Queries are the primary way to interact with a PostgreSQL database and retrieve or manipulate data stored within its tables. In this section, we will cover the fundamentals of querying in PostgreSQL - from basic SELECT statements to more advanced techniques like joins, subqueries, and aggregate functions.

Querying a Table 
To retrieve data from a table, the table is queried. An SQL SELECT statement is used to do this. The statement is divided into a select list (the part that lists the columns to be returned), a table list (the part that lists the tables from which to retrieve the data), and an optional qualification (the part that specifies any restrictions). For example, to retrieve all the rows of table weather, type:

SELECT * FROM weather;
Here * is a shorthand for “all columns”. [2] So the same result would be had with:

SELECT city, temp_lo, temp_hi, prcp, date FROM weather;
The output should be:

     city      | temp_lo | temp_hi | prcp |    date
---------------+---------+---------+------+------------
 San Francisco |      46 |      50 | 0.25 | 1994-11-27
 San Francisco |      43 |      57 |    0 | 1994-11-29
 Hayward       |      37 |      54 |      | 1994-11-29
(3 rows)
You can write expressions, not just simple column references, in the select list. For example, you can do:

SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;
This should give:

     city      | temp_avg |    date
---------------+----------+------------
 San Francisco |       48 | 1994-11-27
 San Francisco |       50 | 1994-11-29
 Hayward       |       45 | 1994-11-29
(3 rows)
Notice how the AS clause is used to relabel the output column. (The AS clause is optional.)

A query can be “qualified” by adding a WHERE clause that specifies which rows are wanted. The WHERE clause contains a Boolean (truth value) expression, and only rows for which the Boolean expression is true are returned. The usual Boolean operators (AND, OR, and NOT) are allowed in the qualification. For example, the following retrieves the weather of San Francisco on rainy days:

SELECT * FROM weather
    WHERE city = 'San Francisco' AND prcp > 0.0;
Result:

     city      | temp_lo | temp_hi | prcp |    date
---------------+---------+---------+------+------------
 San Francisco |      46 |      50 | 0.25 | 1994-11-27
(1 row)
You can request that the results of a query be returned in sorted order:

SELECT * FROM weather
    ORDER BY city;
     city      | temp_lo | temp_hi | prcp |    date
---------------+---------+---------+------+------------
 Hayward       |      37 |      54 |      | 1994-11-29
 San Francisco |      43 |      57 |    0 | 1994-11-29
 San Francisco |      46 |      50 | 0.25 | 1994-11-27
In this example, the sort order isn't fully specified, and so you might get the San Francisco rows in either order. But you'd always get the results shown above if you do:

SELECT * FROM weather
    ORDER BY city, temp_lo;
You can request that duplicate rows be removed from the result of a query:

SELECT DISTINCT city
    FROM weather;
     city
---------------
 Hayward
 San Francisco
(2 rows)
Here again, the result row ordering might vary. You can ensure consistent results by using DISTINCT and ORDER BY together: [3]

SELECT DISTINCT city
    FROM weather
    ORDER BY city;
