Relational Model
The relational model is an approach to organizing and structuring data using tables, also referred to as “relations”. It was first introduced by Edgar F. Codd in 1970 and has since become the foundation for most database management systems (DBMS), including PostgreSQL. This model organizes data into tables with rows and columns, where each row represents a single record and each column represents an attribute or field of the record.


The Relational Model represents data and their relationships through a collection of tables. Each table also known as a relation consists of rows and columns. Every column has a unique name and corresponds to a specific attribute, while each row contains a set of related data values representing a real-world entity or relationship. This model is part of the record-based models which structure data in fixed-format records each belonging to a particular type with a defined set of attributes.
E.F. Codd introduced the Relational Model to organize data as relations or tables. After creating the conceptual design of a database using an ER diagram, this design must be transformed into a relational model which can then be implemented using relational database systems like Oracle SQL or MySQL.
What is the Relational Model? 
The relational model represents how data is stored in Relational Databases. A relational database consists of a collection of tables each of which is assigned a unique name. Consider a relation STUDENT with attributes ROLL_NO, NAME, ADDRESS, PHONE, and AGE shown in the table. 

Key Terms
Attribute: Attributes are the properties that define an entity. e.g. ROLL_NO, NAME, ADDRESS.
Relation Schema: A relation schema defines the structure of the relation and represents the name of the relation with its attributes. e.g. STUDENT (ROLL_NO, NAME, ADDRESS, PHONE, and AGE) is the relation schema for STUDENT. If a schema has more than 1 relation it is called Relational Schema.
Tuple: Each row in the relation is known as a tuple. The above relation contains 4 tuples one of which is shown as:
1	RAM	DELHI	9455123451	18
Relation Instance: The set of tuples of a relation at a particular instance of time is called a relation instance. It can change whenever there is an insertion, deletion or update in the database.
Degree: The number of attributes in the relation is known as the degree of the relation. The STUDENT relation defined above has degree 5.
Cardinality: The number of tuples in a relation is known as cardinality. The STUDENT relation defined above has cardinality 4.
Column: The column represents the set of values for a particular attribute. The column ROLL_NO is extracted from the relation STUDENT.
NULL Values: The value which is not known or unavailable is called a NULL value. It is represented by NULL. e.g. PHONE of STUDENT having ROLL_NO 4 is NULL. 
Relation Key: These are basically the keys that are used to identify the rows uniquely or also help in identifying tables. These are of the following types:
Primary Key
Candidate Key
Super Key
Foreign Key
Alternate Key
Composite Key
